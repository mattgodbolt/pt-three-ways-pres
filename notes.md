# Intro

* Hello who I am, why I care
  * Used to work in games
  * gfx is now an occasional hobby
  * **definitely not an expert**
  * get asked from time to time how to get good at languages
  * my go-to is "write something you love" in language
  * often that's a path tracer or emulator
  * decided to try "learning" C++ again by writing path tracer in three styles

---

* What's a path tracer?
  * Way of generating photorealistic images by tracing the path of light rays as they interact with a scene
  * Similar to ray tracing but instead of special casing lights, all surfaces are potentially emissive (work on this)
  * some pictures needed here, maybe showing difference between  PT and RT too?
  * Best of all, the code to a PT is pretty straightforward
  * Show smallpt
  
---

* What are the styles
  * I AM NO EXPERT IN FP OR DOD

---

# Object-Oriented
* Overall design
* Testability
* Performance
* Show devirtualisation in CE?
---

# Functional
* Design
* Testability
* Performance
* Admission about `std::optional<>` (maybe?)
* Observations; found bugs using it (see below), plus perf benefits
---

# Data-Oriented Design
* Explain
* Expectations, will be fastest!
* Testability
* Performance?

---

# Yay?
* Side-by-side image comparison? Perf comparison?

---

# How would we make it go quicker?
* Profile
* Show that "bottleneck" is a FP DIV for 1.0
  * "how can we make this faster?"
  * could go down rabbit hole of fast recip in float-land, but...
* Maybe we could intersect fewer triangles in the first place?

---

# K-d trees
* Show
* Retrofit into each, how much work
* Final figures for perf and 

# Conclusion
* C++ is multi-paradigm
* No one style is best, but we're lucky we can pick the best bits of each
* Final version?
* Rendered CE logo (yay)

# Thanks to
* SmallPt
* Michael Fogleman's go pt
* (attribution for images, backgrounds, using Romain's stuff again?)
---

# Monkeys
# Badgers

---

* bugs found in FP
* FP helped find an "extract method" which led to removing redundant creation of ONBs (960b421)

* stupid AWS no-C++ compiler so Cmake set empty flags (first beast aws)

Profile of suzanne:

```
Overhead  Command          Shared Object        Symbol
  90.63%  pt_three_ways    pt_three_ways        [.] oo::(anonymous namespace)::TrianglePrimitive::intersect
   7.01%  pt_three_ways    pt_three_ways        [.] oo::Scene::intersect
   0.70%  pt_three_ways    libm-2.27.so         [.] __sincos
   0.31%  pt_three_ways    pt_three_ways        [.] oo::Renderer::render(std::function<void ()>) const::{lambda()#2}::operator()
```


## DOD notes

* "Deconstructed" (make analogy to chefs?)


OVERALL NOTES
* show OO
* show FP (make note that OO could benefit from learnings of FP)
* show DoD (make note that this uses some things learned, and can feed back) e.g. radius squared
  * dod seems harder to test?
