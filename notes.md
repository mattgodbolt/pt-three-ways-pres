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

* My path tracer
  * Spheres and triangles
  * Diffuse (TODO & specular?)
  * Reflection
  * "Ways" conform to simple common scene builder API and output

---

* What are the styles
  * I AM NO EXPERT IN FP OR DOD

---

# Object-Oriented
* Overall design
  * Scene graph with a base class
  * Show base class
  * Renderer object that takes a Scene and a camera
* Testability
  * Specific tests for sphere, triangle
  * `mocked` out tests?
* Performance
  * TODO
  * Show devirtualisation in CE?
* Build time

---

# Functional
* Design
* Testability
* Performance
* Build time
* Admission about `std::optional<>` (maybe?)
* Observations; found bugs using it (see below), plus perf benefits
---

# Data-Oriented Design
* Explain
* Expectations, will be fastest!
* Testability
* Performance?
* Build time

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
* Noticed code imporved as I went on
* Added strong Norm type and was able to remove few unnecessary tests
  - and was able to use it to discover unnecessary normalisation!

```
C++ is a multi-paradigm language allowing us as developers to pick and choose among a variety of styles: procedural, functional, object oriented, hybrids, and more. How does the style of programming we choose affect code clarity, testability, ease of changes, compile time and run-time performance? 

In this talk Matt will show a toy path tracer project (a form of ray tracer) implemented in three different styles: traditional object oriented, functional, and data-oriented design. He'll then compare and contrast his experiences developing in each case, showing how often the compiler is able to reduce each style to similar performing code. There's certain to be some surprises - and of course some Compiler Explorer usage!
```

... argh

```
sorry, unimplemented: mangling reference_type
```