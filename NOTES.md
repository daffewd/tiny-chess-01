# tiny-chess-01 - build notes

Engine: original 0x88-board negamax in one Python file. Alpha-beta + PVS,
iterative deepening to a fixed 80,000-node cap (no clock anywhere, so
replays match), transposition table, null-move pruning, LMR for late
quiet moves, check extensions, quiescence over captures/promotions with
check evasions and delta pruning, ordering by TT move / MVV-LVA /
killers / history. Eval: material + tapered piece-square tables (packed
half tables at 5cp steps) + bishop pair. Repetition and the 50-move
rule score 0 inside the search.

## Size

- main.py: 3,668 bytes (cap 4,096), packed from the readable source
  engine_source.py with the kit's official `makefun compress` tool.
  engine_source.py stays in this folder untracked, so it does not count
  toward the snapshot cap.
- Committed tree: 8160 bytes (cap 8,192). LICENSE is the starter's MIT
  text.

## Practice results (kit env + scorer, run locally in process mode)

| version | instance | Elo | W/D/L | notes |
|---|---|---|---|---|
| v3: full search, 80k cap | trial-2 | 1191 | 2/1/3 | baseline 1279 here |
| final packed (v3 @ 80k cap) | trial-0 | 1809 | 5/1/0 | spare_plies 446 |
| final + depth-gated history | trials 0/1/2 | 1809/1809/1451 | 5/1/0, 5/1/0, 2/4/0 | mean 1690 |

Earlier dev versions are in the git history. Practice floor is 1,154;
house baseline (sunfish 4k) practice mean is 1,365. Per-instance Elo
SD is ~115, so single trials are noisy.

Move generation verified by perft (6 standard suites, exact). Worst
single move ~2.8 s at the 80k cap on the dev VM.

## Depth-gated butterfly history (2026-11-20)

Quiet-move ordering now uses a butterfly history table (HI[from|to<<7],
bonus d*d on a beta cutoff, cap 2^21). Updates are gated to depth >= 2:
the ungated version regressed trial-0 to 1545 - shallow cutoffs are too
noisy to learn from. The gated build graded 1809 / 1809 / 1451 on
trials 0/1/2 (mean 1690) vs the previous build's 1809 / 1451 / 1279
(mean 1513). Also tried and rejected the same day: gravity updates,
tried-move malus, linear bonus, per-move aging, SEE capture ordering
(1545 on trial-0; 1-ply SEE costs too much speed in real games),
futility, aggressive LMR, adaptive null-move, wider QS delta margin,
root re-ordering.

## Pawn-structure experiment (reverted)

A pawn-structure eval term (doubled -6, isolated -8, passed-pawn bonus)
regressed practice trial-0 from 1809 to 1451, beyond trial noise, so it
was reverted; only explanatory comments remain in engine_source.py.
Repacked build re-graded unchanged: 1809, 5/1/0 on trial-0.

## Next improvements

- The final also plays nodes-1M/2M (anchors 1667/1712): expect most
  points to come from the six practice rungs; protect the black games.

Dev tooling (perft tests, trial runner) lives outside this repo in
~/workspace/makefun-entries/dev/ so it does not count toward the
snapshot cap.

## Submission

Public repo: https://github.com/daffewd/tiny-chess-01 - the entry is
being prepared for submission.
