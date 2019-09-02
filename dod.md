<div class="white-bg">

# Style 3
## Data-oriented Design

</div>

---

* Design around most common operations
* Design data by access patterns
* Avoid branches
* Polymorphism by separation

---

### Changes

* Intersection, most common op
  * Only need nearest
  * Very often doesn't intersect, or further away
* Two types of object
  * Separate into two lists


---

### Scene

```cpp
class Scene {
  std::vector<TriangleVertices> triangleVerts_;
  std::vector<TriangleNormals> triangleNormals_;
  std::vector<Material> triangleMaterials_;

  std::vector<Sphere> spheres_;
  std::vector<Material> sphereMaterials_;
```

---

### Intersection

```cpp
std::optional<IntersectionRecord>
Scene::intersectSpheres(const Ray &ray, double nearerThan) const {
  double currentNearestDist = nearerThan;
  std::optional<size_t> nearestIndex;
  for (size_t sphereIndex = 0; sphereIndex < spheres_.size(); ++sphereIndex) {
    // Solve t^2*d.d + 2*t*(o-p).d + (o-p).(o-p)-R^2 = 0
    auto op = spheres_[sphereIndex].centre - ray.origin();
    auto b = op.dot(ray.direction());
    auto determinant =
        b * b - op.lengthSquared() + spheres_[sphereIndex].radiusSquared;
    if (determinant < 0)
      continue;

    determinant = sqrt(determinant);
    auto minusT = b - determinant;
    auto plusT = b + determinant;
    if (minusT < Epsilon && plusT < Epsilon)
      continue;

    auto t = minusT > Epsilon ? minusT : plusT;
    if (t < currentNearestDist) {
      nearestIndex = sphereIndex;
      currentNearestDist = t;
    }
  }
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

### Rest broadly the same

* Rendering improvements

---

notes
* was third time I wrote this. had already thought about how to
 improve
* many techniques here would work in the other ways
* IntersectionRecord - `const Material &` with optional?
