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

---

### First basement runthrough notes

* Timings
  - 7m to end of PT dec
  - 12m to Styles
  - 17m to beginning of OO
  - 27m at sphere primt
  - 32 at FP
  - 45m to slide 5.9 (DoD scene intersect)
  - 55m end
* Notes
  - Intro the 3 ways earlier?
  - maybe consider render loop being first? 3.4
  - highlightes on sphere prim
  - Talk about Ranges and TL library
  - add pics on conclusion
  
  
---

### first actual runthrough

* No timings, went well over
* Notes
  - Make OO more "OO" not enough of a caricature
    - Use material() virtual methods for different types
    - accessors()s for all
  - Maybe drop long-winded explanation of the types
  - CULL CULL CULL
- Fixed the perf issue!!!!
  - Get the `perf` results! BPU story is awesome

---

### TODOs

- [ ] OO-ify material
- [ ] more renderings! pictures everywhere
- [ ] do drawings of PT slides
- [ ] re-profile with and without the BPU fix
- [ ] BPU slides

---

BPU notes:

* 128x128 suzanne 8spp
* perf stat
  - oo ->
```
    93,163,010,466      cycles                    
   252,203,645,149      instructions              #    2.71  insn per cycle         
    23,158,824,048      branches                  #  845.438 M/sec                  
       139,741,218      branch-misses             #    0.60% of all branches        
```
  - fp ->
```
   134,463,155,131      cycles                    
   238,866,691,159      instructions              #    1.78  insn per cycle         
    21,881,070,251      branches                  #  560.513 M/sec                  
     1,105,066,725      branch-misses             #    5.05% of all branches        
```
  - dod -> 
```
   115,112,159,658      cycles                    
   154,821,779,748      instructions              #    1.34  insn per cycle         
    10,353,392,805      branches                  #  305.213 M/sec                  
     1,242,670,094      branch-misses             #   12.00% of all branches        
```
* perf record -e branch-misses:p ...
* bpu-fail.png
* http://localhost:10240/#z:OYLghAFBqd5QCxAYwPYBMCmBRdBLAF1QCcAaPECAM1QDsCBlZAQwBtMQAmAOk4GZSAKxABGUq2a1QyAKScAQnPmkAzqgCuxZBwDkmAB4FMxWgGp0GgEbtTLVsgCqEC%2BuuYAlDL6KADAEEDIxNzKxs7ZAA1Z1CPL18Aw2MzFzdbNmQAETwVAmjXdk9vGX9ivzx6U1pMZmJMHIg0WhyQ/MxTACpmUlNygkr1AFt3UxkAdnjTSZbUqpq6gizmrwzTEUwRHzjSqZ6K2dqcgElaLH0RvhWAWhEt/x3tqZpiUwhenvOVze93rwBhfoGcRGCiUeGGYwmOymKRsBAQ2Ww7AGH1MzBkAFZ5LRBhiMrc/FCpsx1ERTOoUeEnHCEUjCpDCQ9CXgqC9yX9TD5uD4RqNfmN/my%2BNhVlzPHdCVDGgRyupMPiJSNxYTiaSAG4U9JRakqRGYIb4xlQ5kvdXsznc/n8snA%2BSmU1CkU%2BMUEhVTKUyuVFJVQw07FWoUzaxZ9ZZpezBiDa3X6r0upksyPwlTB87/fbzYPg8a%2BiXpnIp0NB7IEeWuyZ5gjHU4ovClhmjPHesaNuOmWoETRmCtVgwGhs6dziEA6dE6Ui0Yc%2BMeoYd8kEKUxqTTaYF8ERjgiTgeDgDWIHRPiHOgALGOJzop6QZzoxyoQIfNxeB6Q4LAkGgBgAHPDsMgUBqoF%2BP7GCAwAAJxiFQP5BHeECWFupCWOUNQAJ7DuupAfgMmD0AA8rQrBoU%2BpBYAMkjAOwCH4LUyDSqqdQIQYmDICSugYb0mCsAhrB4JYxCob8GBsRuxB4AM6HPjQ9BMGwHA8Pw4jkSgShKOIvF3pAg6oJ%2B0p0HeOiXLhVDMDkt4aFoHAiIOXEjmeCHXvoAAcABslzOcepjAMgyCmGB3AiC8uCECQq5iKYglAb%2BoXDHOigKBuW7uLu%2B6HjZp7jvZw63vepCPlOSVHpwdnEdeCVPgV9HECoeB0CAx5AA%3D