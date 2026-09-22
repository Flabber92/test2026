# Machine Philosophy

## Two memories

- **RECORD** — current world-state. Reloadable, reversible. What
  happened in this playthrough right now.
- **IMPRESSION** — what the machine has ever encountered, across
  reloads. Persistent, not reversible. Consolidates over real elapsed
  time (see Epoch Drift) the way human memory does: specifics decay
  into gist, not into noise.

Reload restores RECORD. It never restores IMPRESSION. The machine can
hesitate on a name it has already processed in a world where that
person died, even in a timeline where they're currently alive.

## Dimensions (not stats — coordinates)

- **Mnemosis** — how much the machine remembers you specifically.
- **Coherence** — grammatical/logical tightness of its output. Falling
  coherence produces denser, more precise fragments, not garbage.
- **Depth** — how far into the machine you currently are. Not
  progress; you can surface and redescend. Depth-7 things are
  invisible from Depth-2, and Depth-2 statements reread from Depth-7
  look different.
- **Pressure** — what the machine is doing to the space it describes.
  High pressure compresses sentences about open places into short,
  claustrophobic ones.
- **Epoch Drift** — the machine's internal clock, unrelated to real
  time, driven by real elapsed time. Present tense becomes past tense
  while you were away. You read the aftermath, never the event.

## Epistemic status (the ontology that matters most)

Every statement the machine holds carries a status, not just content:

`OBSERVED > REPORTED > INFERRED > CONSISTENT > CONTRADICTED > UNRESOLVED > RETROACTIVE`

RETROACTIVE is the important one: a statement that only became
decidable because something *later* happened. The words never change;
a small indicator beside the sentence lights up. This is how future
actions resolve past ambiguity without rewriting the past — the
mechanic that matters most in this whole design.

## Deposition grammar (verdict vocabulary)

The machine never says "wrong." It replies to propositions and
theories with a small fixed vocabulary, each consistently defined and
eventually learnable by the player — except one, by design:

- `STIPULATED` — established, not worth contesting further.
- `DISPUTED` — active, unresolved contradiction.
- `SUSTAINED` — your theory holds against current evidence.
- `OVERRULED` — your theory is incompatible with something stronger.
- `REDACTED` — exists, withheld. The withholding itself is evidence.
- `SEALED` — the one deliberately undefined term. Its rule is never
  stated anywhere, including here. This is the single irreducible
  ambiguity the design protects; do not add a second one.

## The glyph code (a spread, not a page number)

The header code (e.g. `⟁03-2 ⊚1 ᚴ91`) is not decoration and not a page
number. It is a small number of fixed *positions* (Origin, Obstacle,
Hidden, Outcome, ...), each a glyph whose value is a compressed
projection of specific state variables. Players build literacy in the
positions over many sessions — "the second position changed, that's
Hidden, something contradicted became visible." The mapping from
state to glyph is deterministic and fixed at build time; never
randomized at runtime.

## Language decay

Machine prose is drawn from a fixed, authored vocabulary set per Depth
band. At low Depth, full sentences. At high Depth, the same
propositions are expressed through a shrinking root-word set (~200
roots at the deepest band), recombined. This is diegetic difficulty:
learning to read the machine's contracted grammar is real player
progress, and it is entirely pre-authored — no generative text at
runtime.

## Anomalies are never decorative (Bram's law)

Every flicker, gear-stutter, extra rotation, or word-swap must map to
a real internal cause, defined once in `docs/09_GLITCH_GRAMMAR.md`
(to be written alongside implementation). Players will not discover
every cause. We always know it. No random cosmetic "spooky" effects.

## Anti-datamining (Kessy's constraint)

Where a variable is genuinely underdetermined by authored logic, the
tiebreaker is a hash of the real wallclock timing/order of the
player's own actions (not a random seed, not content-visible). A
full text/string dump describes the space of possibilities; it cannot
predict any individual playthrough's collapse. Never resolve an
otherwise-undetermined variable with a runtime RNG call whose seed is
visible or replayable from extracted assets alone.

## Legend / accessibility (ships in MVP, not later)

An always-available, opt-in plain-text legend explains glyph
positions, verdict vocabulary, and provides a non-color-dependent
(pattern/texture) alternative to any "red lens" mechanic, plus full
text equivalents for any audio-only tell. Mystery lives in content and
epistemic structure, never in excluding a player from the interface
itself.
