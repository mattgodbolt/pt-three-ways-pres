# Conclusions <!-- .element: class="white-bg" -->

---

### Favourite?

* All of the above!!

---

down the line?
# purposefully no acceleration structures

* k-d tree means subset? O(N) vs O(log N)
* Dod: first bounce: defer next radiance call?
  - make a queue of pixel/Ray/depth
  
---

<div class="white-bg">

### Performance

```
[77852.398974] mce: CPU7: Package temperature above threshold, cpu clock throttled (total events = 144933)
[77852.398975] mce: CPU3: Package temperature above threshold, cpu clock throttled (total events = 144933)
```

* Amazon 96-core instance
  - Intel(R) Xeon(R) Platinum 8275CL CPU @ 3.00GHz
* couldn't do `sudo cpupower frequency-set --governor performance`
* Single threaded

</div>


---

<div class="white-bg">

TODO: redo
* Cornell box scene 8spp (1 sphere, 38 triangles)
* OO: 18 / 18 / 18 
* fp: 24 / 24 / 24
* DoD: 19 / 19 / 19

</div>

---

<div class="white-bg">

TODO: redo

* Suzanne 8spp (2 spheres, 970 triangles)
* OO: 90
* fp: 145
* DoD: 117

</div>

---
<div class="white-bg">

TODO: redo

* Owl scene 128spp (100 spheres, 12 triangles)
* OO: 76 / 76 / 77
* fp: 115 / 116 / 115
* DoD: 64 / 64 / 64

</div>

---

<div class="white-bg">

TODO: redo

* CE logo
* OO:
* fp:
* DoD:

</div>


---

<div class="white-bg">

WRITE BENCHMARKS

* divide/normalisation stuff
* BENCHMARKING IS HARD!!!
  - tried benchmarking 1/x vs /x and ... looked faster until I
    fixed benchmark (x/x == 1)
  - https://godbolt.org/z/VJZEYb

</div>

---

<div class="white-bg">
 
* MYSTERY OF THE SLOW TRIANGLES in DoD
 * unhelpful profile...
 
```
  98.49%  pt_three_ways  pt_three_ways        [.] dod::Scene::intersect
   0.61%  pt_three_ways  libm-2.27.so         [.] __sincos
   0.20%  pt_three_ways  pt_three_ways        [.] dod::Scene::render(
```

</div>

---

<div class="white-bg">

### If only I had more time...

</div>