<div class="white-bg">

# Style 1
## Object Oriented

</div>

---

<pre><code class="cpp" data-trim data-noescape>
class Primitive {
public:
  virtual ~Primitive() = default;
  
<div class="fragment highlight-current-code">  struct IntersectionRecord {
    Hit hit;
    Material material;
  };
</div>
  [[nodiscard]] <span class="fragment highlight-current-code">virtual</span> bool intersect(
    const Ray &ray, 
    <span class="fragment highlight-current-code">IntersectionRecord &intersection</span>) const = 0;
};
</code></pre>

<aside class="notes">
SOME NOTESES
</aside>

---

<pre><code class="cpp" data-trim data-noescape>
<div class="fragment highlight-current-code" data-fragment-index="1">for (int y = 0; y < height; ++y) {
  for (int x = 0; x < width; ++x) {
</div>    Vec3 colour;
<div class="fragment highlight-current-code" data-fragment-index="2">    for (int sample = 0; sample < numSamples; ++sample) {
</div>      <span class="fragment highlight-current-code" data-fragment-index="3">auto ray = camera_.randomRay(x, y, rng);</span>
      colour += <span class="fragment highlight-current-code" data-fragment-index="4">radiance(rng, ray, NumUSamples, NumVSamples);</span>
<div class="fragment highlight-current-code" data-fragment-index="2">    }
</div>    <span class="fragment highlight-current-code" data-fragment-index="5">output.plot(x, y, colour / numSamples);</span>
<div class="fragment highlight-current-code" data-fragment-index="1">  }
}
</div>
</code></pre>

---

### Radiance <!--- .element: class="white-bg" --->

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

      auto nextPath = material.bounce(hit, ray, u, v, p);
      result += nextPath.colour * 
            radiance(rng, nextPath.bounced, depth + 1);
    }
  }
```

---
```cpp
  return material.emissive + result / (numUSamples * numVSamples);
}
```

---

### Intersection <!--- .element: class="white-bg" --->

---

```cpp
struct SpherePrimitive : Primitive {
  Sphere sphere;
  Material material;
  SpherePrimitive(const Sphere &sphere, const Material &material)
      : sphere(sphere), material(material) {}
  [[nodiscard]] bool intersect(const Ray &ray,
                               IntersectionRecord &rec) const override {
    Hit hit;
    if (!sphere.intersect(ray, hit))
      return false;
    rec = IntersectionRecord{hit, material};
    return true;
  }
};
```

---

```cpp
bool Sphere::intersect(const Ray &ray, Hit &hit) const noexcept {
  // Solve t^2*d.d + 2*t*(o-p).d + (o-p).(o-p)-R^2 = 0
  auto op = centre_ - ray.origin();
  auto radiusSquared = radius_ * radius_;
  auto b = op.dot(ray.direction());
  auto determinant = b * b - op.lengthSquared() + radiusSquared;
  if (determinant < 0)
    return false;

  determinant = sqrt(determinant);
  auto minusT = b - determinant;
  auto plusT = b + determinant;
  if (minusT < Epsilon && plusT < Epsilon)
    return false;

  auto t = minusT > Epsilon ? minusT : plusT;
  auto hitPosition = ray.positionAlong(t);
  auto normal = (hitPosition - centre_).normalised();
  bool inside = normal.dot(ray.direction()) > 0;
  if (inside)
    normal = -normal;
  hit = Hit{t, inside, hitPosition, normal};
  return true;
}
```

---

```cpp
bool Triangle::intersect(const Ray &ray, Hit &hit) const noexcept {
  auto pVec = ray.direction().cross(vVector());
  auto det = uVector().dot(pVec);
  // ray and triangle are parallel if det is close to 0
  if (fabs(det) < Epsilon)
    return false;

  auto backfacing = det < Epsilon;

  auto invDet = 1.0 / det;
  auto tVec = ray.origin() - vertices_[0];
  auto u = tVec.dot(pVec) * invDet;
  if (u < 0.0 || u > 1.0)
    return false;

  auto qVec = tVec.cross(uVector());
  auto v = ray.direction().dot(qVec) * invDet;
  if (v < 0 || u + v > 1)
    return false;

  auto t = vVector().dot(qVec) * invDet;

  if (t < Epsilon)
    return false;

  auto normalUdelta = normals_[1] - normals_[0];
  auto normalVdelta = normals_[2] - normals_[0];
  // TODO: proper barycentric coordinates
  auto normal =
      ((u * normalUdelta) + (v * normalVdelta) + normals_[0]).normalised();
  if (backfacing)
    normal = -normal;
  hit = Hit{t, backfacing, ray.positionAlong(t), normal};
  return true;
}
```

---

## Materials <!--- .element: class="white-bg" --->

---

```cpp
Material::Bounce Material::bounce(const Hit &hit, const Ray &incoming, double u,
                                  double v, double p) const {
  double iorFrom = 1.0;
  double iorTo = mat_.indexOfRefraction;
  auto reflectivity = mat_.reflectivity;
  if (hit.inside) {
    std::swap(iorFrom, iorTo);
  }
  if (reflectivity < 0) {
    reflectivity = hit.normal.reflectance(incoming.direction(), iorFrom, iorTo);
  }
  if (p < reflectivity) {
    return Bounce{
        Vec3(1, 1, 1),
        Ray(hit.position, coneSample(hit.normal.reflect(incoming.direction()),
                                     mat_.reflectionConeAngleRadians, u, v))};
  } else {
    auto basis = OrthoNormalBasis::fromZ(hit.normal);
    return Bounce{mat_.diffuse,
                  Ray(hit.position, hemisphereSample(basis, u, v))};
  }
}
```

---

<div class="white-bg">

### Things I liked

* Code layout
* TODO

</div>
