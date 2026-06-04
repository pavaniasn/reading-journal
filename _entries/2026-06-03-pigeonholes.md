---
date: 2026-06-03
title: "Pigeonholes, Birthday Paradoxes, and Why Randomness Leaves Gaps"
tag: Ideas
author: "Class session"
source: "Probability and computer science concepts"
source_url: ""
summary: |
  Three connected ideas from a class session on probability and hashing. First: the pigeonhole principle — if you have more pigeons than holes, at least one hole must hold more than one. Clean and obvious. Second: the birthday paradox — you need only 23 people in a room for there to be a better-than-even chance two share a birthday, because we should be counting pairs, not individuals. Third: throw 100 balls randomly into 100 bins and expect even distribution — but randomness doesn't work that way. Some bins get multiple balls, some stay empty. Scale to 1,000 balls or 10,000 and the number of overpicked bins grows, but only by a digit. This is exactly why hashing in computer science must account for collisions — they are not edge cases, they are the expected behavior of randomness. And the number of empty bins in these distributions converges, in the limit, to a value involving *e* (Euler's number, ≈ 2.718) — the same constant that governs compound interest and natural growth.
notes: |
  I came in as a non-technical person and the part that genuinely surprised me was the appearance of *e*. Not "approximately" — actually converges to it. The fact that the same mathematical constant shows up in compound interest, natural logarithms, and in how randomness fills empty bins feels like it's pointing at something true about the structure of reality, not just the math. I didn't follow the full derivation, but the high-level idea landed.
---
