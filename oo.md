<div class="white-bg">

## General approach

* Shared basic math library
  * `Vec3`
  * `Ray`
  * `Hit`
* Some simple "util" classes
* Shared `Material` classes

</div>

---
```cpp
class Vec3 {
  double x_{}, y_{}, z_{};

public:
  constexpr Vec3() noexcept = default;
  constexpr Vec3(double x, double y, double z) noexcept 
    : x_(x), y_(y), z_(z) {}

```

---
```cpp
  constexpr Vec3 operator+(const Vec3 &b) const noexcept {
    return Vec3(x_ + b.x_, y_ + b.y_, z_ + b.z_);
  }
  // ...and other operators, dot products, cross products, etc...

  [[nodiscard]] constexpr double x() const noexcept { return x_; }
  [[nodiscard]] constexpr double y() const noexcept { return y_; }
  [[nodiscard]] constexpr double z() const noexcept { return z_; }
};

```

---
<div class="white-bg">

# Style 1
## Object Oriented

</div>

---
```cpp
class Primitive {
public:
  virtual ~Primitive() = default;

  struct IntersectionRecord {
    Hit hit;
    Material material;
  };

  [[nodiscard]] virtual bool intersect(
    const Ray &ray, 
    IntersectionRecord &intersection) const = 0;
};
```

<aside class="notes">
SOME NOTESES
</aside>

---
```cpp
for (int y = 0; y < heignt; ++y) {
  for (int x = 0; x < width; ++x) {
    Vec3 colour;
    for (int sample = 0; sample < numSamples; ++sample) {
      auto ray = camera_.randomRay(x, y, rng);
      colour += radiance(rng, ray, NumUSamples, NumVSamples);
    }
    output.plot(x, y, colour / numSamples);
  }
}
```

---
```cpp
Vec3 Renderer::radiance(
    std::mt19937 &rng, const Ray &ray, int depth,
    int numUSamples, int numVSamples) const {

  if (depth >= MaxDepth)
    return Vec3();

  Primitive::IntersectionRecord intersectionRecord;
  if (!scene_.intersect(ray, intersectionRecord))
    return scene_.environment(ray);
```

---
```cpp
  const auto &material = intersectionRecord.material;
  const auto &hit = intersectionRecord.hit;

  Vec3 result;
  std::uniform_real_distribution<> unit(0, 1.0);
```

---
```cpp
  for (auto uSample = 0; uSample < numUSamples; ++uSample) {
    for (auto vSample = 0; vSample < numVSamples; ++vSample) {
      auto u = (uSample + unit(rng)) / numUSamples;
      auto v = (vSample + unit(rng)) / numVSamples;

      auto p = unit(rng);

      auto newRay = bounce(material, hit, ray, u, v, p);

      result += radiance(rng, newRay, depth + 1, 1, 1);
    }
  }
```

---
```cpp
  return material.emissive + 
    material.diffuse * result / (numUSamples * numVSamples);
}
```