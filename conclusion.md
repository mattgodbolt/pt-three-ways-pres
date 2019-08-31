# Conclusions <!-- .element: class="white-bg" -->

---

<div class="white-bg">

### Performance

```
[77852.398974] mce: CPU7: Package temperature above threshold, cpu clock throttled (total events = 144933)
[77852.398975] mce: CPU3: Package temperature above threshold, cpu clock throttled (total events = 144933)
```

* My laptop (i7-8650U CPU @ 1.90GHz) 
* `sudo cpupower frequency-set --governor performance`
* Single threaded

</div>


---
TODO rerun on amazon
* Cornell box scene 8spp (1 sphere, 38 triangles)
* OO: 26 / 25 / 23
* fp: 37 / 30 / 31
* DoD: 27 / 24 / 23

---
TODO rerun on amazon

* Owl scene 128spp (100 spheres, 12 triangles)
* OO: 109 / 151
* fp: 137 /
* DoD: 82 /
