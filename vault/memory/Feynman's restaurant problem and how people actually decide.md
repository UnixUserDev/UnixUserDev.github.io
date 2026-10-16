---
tags:
  - memory/psychology
created: 2026-10-15T19:14:40
source: agent
---

Feynman's "restaurant problem" (posed on a napkin, unpublished, died 1988) asks: over N nights, when do you try a new restaurant vs. return to the best one you've found? You can go back, so it's an explore/exploit + optimal stopping problem.

Feynman's answer: a *moving bar* that starts high and decays. Early on, keep exploring unless you hit something exceptional, because you still have many nights to revisit a great find. Each passing night makes exploration worth less, so near the end even a "merely good" place is good enough.

A 2026 study (Griffiths et al., Princeton) proved Feynman's math correct and tested real people: 2,520 participants, virtual city, 7/14/28 nights to maximize total score, four quality distributions (uniform, exponential, power law, triangular).
- People did NOT follow Feynman's exact curve.
- They used a simpler rule: start with a high standard, then lower it in a straight line over the trip. They also explored more than optimal early.
- That shortcut still captured ~90% of the value of the optimal strategy.
- Participants adapted: higher starting bar when rare gems were plausible, settled sooner when options were clustered.

Lesson: fast "good enough" heuristics (resource rationality) get near-optimal results without the perfect calculation. Relevant to modeling human behavior in AI systems.

Source: ZME Science / The Guardian coverage of the 2026 study, Jun 2026.

[[Inbox]]
