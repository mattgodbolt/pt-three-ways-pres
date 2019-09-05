<div class="white-bg">

## Functional Programming

* ranges
* `lt::optional`

</div>

---

### Scene <!-- .element: class="white-bg" -->

```cpp
struct TrianglePrimitive {
  Triangle triangle;
  Material material;
};

struct SpherePrimitive {
  Sphere sphere;
  Material material;
};

using Primitive = std::variant<TrianglePrimitive, SpherePrimitive>;
```

---

### Render <!-- .element: class="white-bg" -->

```cpp
auto renderOnePixel = [...](auto tuple) {
  auto [y, x] = tuple;
  std::mt19937 rng(...);
  return radiance(scene, rng, 
        camera.randomRay(x, y, rng), 0, renderParams);
};
auto renderedPixelsView =
  view::cartesian_product(view::ints(0, renderParams.height),
                          view::ints(0, renderParams.width))
  | view::transform(renderOnePixel);
return ArrayOutput(renderParams.width, renderParams.height,
                 renderedPixelsView);
```

---

### Radiance <!-- .element: class="white-bg" -->

```cpp

const auto intersectionRecord = intersect(scene, ray);
if (!intersectionRecord)
  return scene.environment;
```

---

```cpp
const auto incomingLight = accumulate(
    view::cartesian_product(view::ints(0, numVSamples),
                            view::ints(0, numUSamples))
        | view::transform(toUVSample)
        | view::transform([&](auto s) {
            return singleRay(scene, rng, *intersectionRecord, ray, basis,
                             s.first, s.second, depth + 1, renderParams);
          }),
    Vec3());
return mat.emission + incomingLight / (numUSamples * numVSamples);
```

---

```cpp
auto toUVSample = [&rng, &unit, numUSamples, numVSamples](auto vu) {
  auto [v, u] = vu;
  const auto sampleU = (u + unit(rng)) / numUSamples;
  const auto sampleV = (v + unit(rng)) / numVSamples;
  return std::make_pair(sampleU, sampleV);
};
```

---

### Intersection <!-- .element: class="white-bg" -->

```cpp
struct IntersectVisitor {
  const Ray &ray;

  auto operator()(const TrianglePrimitive &primitive) const {
    return primitive.triangle.intersect(ray)
        .map([&primitive](auto hit) {
            return IntersectionRecord{hit, primitive.material};
        });
  }

  auto operator()(const SpherePrimitive &primitive) const {
    return primitive.sphere.intersect(ray)
        .map([&primitive](auto hit) {
            return IntersectionRecord{hit, primitive.material};
        });
  }
};
```

---

```cpp
std::optional<IntersectionRecord> intersect(const Primitive &primitive,
                                            const Ray &ray) {
  return std::visit(IntersectVisitor{ray}, primitive);
}
```

---

```cpp
tl::optional<Hit> Sphere::intersect(const Ray &ray) const noexcept {
  const auto op = centre_ - ray.origin();
  const auto b = op.dot(ray.direction());
  return sqrtOptional(b * b - op.lengthSquared() + radius_ * radius_)
      .and_then([&b](double determinant) -> tl::optional<double> {
        const auto minusT = b - determinant;
        const auto plusT = b + determinant;
        if (minusT < Epsilon && plusT < Epsilon)
          return tl::nullopt;
        return minusT > Epsilon ? minusT : plusT;
      })
      .map([this, &ray](double t) {
        const auto hitPosition = ray.positionAlong(t);
        const auto normal = (hitPosition - centre_).normalised();
        const bool inside = normal.dot(ray.direction()) > 0;
        return Hit{t, inside, hitPosition, inside ? -normal : normal};
      });
}
```

---

```cpp
std::optional<IntersectionRecord> intersect(const Scene &scene,
                                            const Ray &ray) {
  std::optional<IntersectionRecord> nearest;
  for (auto &primitive : scene.primitives) {
    auto thisIntersection = intersect(primitive, ray);
    if (thisIntersection
        && (!nearest || thisIntersection->hit.distance < nearest->hit.distance))
      nearest = thisIntersection;
  }
  return nearest;
}
```

---

### Materials  <!-- .element: class="white-bg" -->

```cpp
Vec3 singleRay(const Scene &scene, std::mt19937 &rng,
               const IntersectionRecord &intersectionRecord, const Ray &ray,
               const OrthoNormalBasis &basis, double u, double v, int depth,
               const RenderParams &renderParams) {
  std::uniform_real_distribution<> unit(0, 1.0);
  const auto &mat = intersectionRecord.material;
  const auto &hit = intersectionRecord.hit;
  const auto p = unit(rng);

  const auto [iorFrom, iorTo] =
      hit.inside ? std::make_pair(mat.indexOfRefraction, 1.0)
                 : std::make_pair(1.0, mat.indexOfRefraction);
  const auto reflectivity =
      mat.reflectivity < 0
          ? hit.normal.reflectance(ray.direction(), iorFrom, iorTo)
          : mat.reflectivity;

  if (p < reflectivity) {
    const auto newRay =
        Ray(hit.position, coneSample(hit.normal.reflect(ray.direction()),
                                     mat.reflectionConeAngleRadians, u, v));
    return radiance(scene, rng, newRay, depth, renderParams);
  } else {
    const auto newRay = Ray(hit.position, hemisphereSample(basis, u, v));
    return mat.diffuse * radiance(scene, rng, newRay, depth, renderParams);
  }
}
```


---

<div class="white-bg">

### Things I liked

* `const` :allthethings:
* Code seemed clearer?
* Testability notes
* Performance notes
  - rng per pixel to satisfy FP constraints?

</div>
