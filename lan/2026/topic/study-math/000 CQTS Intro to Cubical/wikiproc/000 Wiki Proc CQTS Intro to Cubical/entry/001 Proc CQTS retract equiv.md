---
context_type: entry
---

Parent: [lan/2026/topic/study-math/000 CQTS Intro to Cubical/wikiproc/000 Wiki Proc CQTS Intro to Cubical/000 Wiki Proc CQTS Intro to Cubical](../000%20Wiki%20Proc%20CQTS%20Intro%20to%20Cubical.md)

Spawned by: [lan/2026/topic/study-math/000 CQTS Intro to Cubical/wikiproc/000 Wiki Proc CQTS Intro to Cubical/000 Wiki Proc CQTS Intro to Cubical](../000%20Wiki%20Proc%20CQTS%20Intro%20to%20Cubical.md)

Spawned in: [^spawn-entry-be7615](../000%20Wiki%20Proc%20CQTS%20Intro%20to%20Cubical.md#spawn-entry-be7615)

Overview: [000 Overview Wiki Proc CQTS Intro to Cubical](000%20Overview%20Wiki%20Proc%20CQTS%20Intro%20to%20Cubical.md)

Errata: [002 Errata Proc CQTS retract equiv](002%20Errata%20Proc%20CQTS%20retract%20equiv.md)

Iterations: [004 Iterations for Proc CQTS retract equiv](004%20Iterations%20for%20Proc%20CQTS%20retract%20equiv.md)

# Journal

2026-06-17 Wk 25 Wed - 12:11 +03:00

Spawn [lan/2026/topic/study-math/000 CQTS Intro to Cubical/wikiproc/000 Wiki Proc CQTS Intro to Cubical/entry/002 Errata Proc CQTS retract equiv](002%20Errata%20Proc%20CQTS%20retract%20equiv.md) ^spawn-entry-5508d1

Spawn [000 How do we interpret retract and section? CQTS](../investigation/000%20How%20do%20we%20interpret%20retract%20and%20section%3F%20CQTS.md) ^spawn-invst-acfdc3

2026-06-17 Wk 25 Wed - 23:53 +03:00

Spawn [lan/2026/topic/study-math/000 CQTS Intro to Cubical/wikiproc/000 Wiki Proc CQTS Intro to Cubical/entry/004 Iterations for Proc CQTS retract equiv](004%20Iterations%20for%20Proc%20CQTS%20retract%20equiv.md) ^spawn-entry-9534d2

2026-06-17 Wk 25 Wed - 10:56 +03:00

Okay getting back to this.

We need to solve

````haskell
retract-≡ : {ℓ₀ : Level} → {A B : Type ℓ₀} → (r : B RetractOnto A)
  → {x y : A}
  → (r .section .map x ≡ r .section .map y) RetractOnto (x ≡ y)
````

2026-06-17 Wk 25 Wed - 10:56 +03:00

This is the hint cube:

````
                y — — — — — — — — > y
              / ^                 / ^
      .map  /   |            p  /   |
          /     |             /     |
        x — — — — — — — — > x       |
        ^       |           ^       |                    ^   j
        |       |           |       |                  k | /
        |       |           |       |                    ∙ — >
        |       |           |       |                      i
        |    r (s y)  — — — | — — > y
        |     /             |     /
        |   /               |   /
        | /                 | /
     r (s x) — — — — — — —  x
````

`r (s x)` should mean `r .map (r . section .map x)`.
