# Agent rules for this repository

Read `docs/00_VISION.md` and `docs/01_MACHINE_PHILOSOPHY.md` before
touching runtime code. These are laws, not suggestions:

1. No runtime LLM calls, no network calls, no server. Everything the
   player reads is authored and frozen at build time.
2. The interface never asks the player "what do you do." It asks
   questions or makes observations.
3. Previously shown text is never silently rewritten. Only its
   epistemic status indicator may change (see 01).
4. Every anomaly (flicker, extra gear rotation, stutter) must trace to
   a real, defined internal cause. No decorative randomness.
5. RECORD (world-state) is reloadable. IMPRESSION (machine memory) is
   not, and consolidates toward gist over real elapsed time.
6. Underdetermined variables collapse using a hash of real wallclock
   action timing/order, never a visible or replayable RNG seed.
7. `SEALED` is the one deliberately undefined verdict. Do not define
   it in code comments, docs, or UI text. Do not add a second one.
8. Ship the plain-text legend and non-color-dependent alternates in
   every milestone, not as a post-launch patch.
9. Major scenes/prose are authored content, not system-generated.
   Systems choose which authored fragment is shown; they do not write
   new fragments at runtime.
10. Prefer plain, dependency-free implementations (no framework, no
    build step) unless a milestone specifically requires otherwise.
