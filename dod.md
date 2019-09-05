<div class="white-bg">

## Data-oriented Design

</div>

---

<div class="white-bg">

* Design around most common operations
* Design data by access patterns
* Avoid branches
* Polymorphism by separation

</div>

---

<div class="white-bg">

### Changes

* Intersection, most common op
  * Only need nearest
  * Very often doesn't intersect, or further away
* Two types of object
  * Separate into two lists

</div>

---


### Scene <!-- .element: class="white-bg" -->

```cpp
class Scene {
  std::vector<TriangleVertices> triangleVerts_;
  std::vector<TriangleNormals> triangleNormals_;
  std::vector<Material> triangleMaterials_;

  std::vector<Sphere> spheres_;
  std::vector<Material> sphereMaterials_;
```

---

### Intersection <!-- .element: class="white-bg" -->

```cpp
std::optional<IntersectionRecord>
Scene::intersectSpheres(const Ray &ray, double nearerThan) const {
  double currentNearestDist = nearerThan;
  std::optional<size_t> nearestIndex;
  for (size_t sphereIndex = 0; sphereIndex < spheres_.size(); ++sphereIndex) {
    // Solve t^2*d.d + 2*t*(o-p).d + (o-p).(o-p)-R^2 = 0...
    // <mathsy stuff here>
    // - if doesn't intersect, continue
    // end up with t = distance
    auto t = ...;
    if (t < currentNearestDist) {
      nearestIndex = sphereIndex;
      currentNearestDist = t;
    }
  }
```

---

```cpp
  if (!nearestIndex)
    return {};

  auto hitPosition = ray.positionAlong(currentNearestDist);
  auto normal = (hitPosition - spheres_[*nearestIndex].centre).normalised();
  bool inside = normal.dot(ray.direction()) > 0;
  if (inside)
    normal = -normal;
  return IntersectionRecord{
      Hit{currentNearestDist, inside, hitPosition, normal},
      sphereMaterials_[*nearestIndex]};
}
```

---

```cpp
std::optional<IntersectionRecord> Scene::intersect(const Ray &ray) const {
  auto sphereRec =
      intersectSpheres(ray, std::numeric_limits<double>::infinity());
  auto triangleRec = intersectTriangles(
      ray, sphereRec ? sphereRec->hit.distance
                     : std::numeric_limits<double>::infinity());
  return triangleRec ? triangleRec : sphereRec;
}
```

---

<div class="white-bg">

### Rest broadly the same

</div>

---

<div class="white-bg">

### Things I liked

* `const` :allthethings:
* Code seemed clearer?
* Testability notes
* Performance
  - rng per pixel to satisfy FP constraints?

</div>

notes
* was third time I wrote this. had already thought about how to
 improve
* many techniques here would work in the other ways
* IntersectionRecord - `const Material &` with optional?
