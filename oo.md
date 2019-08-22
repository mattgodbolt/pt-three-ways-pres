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
Vec3 colour;
for (int sample = 0; sample < numSamples; ++sample) {
  auto ray = camera_.ray(x, y, rng);
  colour += radiance(rng, ray);
}
return colour / numSamples;
```