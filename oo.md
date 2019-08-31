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
// TODO update
  return material.emissive + 
    material.diffuse * result / (numUSamples * numVSamples);
}
```