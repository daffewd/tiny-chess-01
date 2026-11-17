# tiny-chess-01 - build notes

Engine: original 0x88-board negamax in one Python file. Alpha-beta + PVS,
iterative deepening to a fixed 80,000-node cap (no clock anywhere, so
replays match), transposition table, null-move pruning (R=2, non-pawn
material guard), LMR for late quiet moves, check extensions, quiescence
over captures/promotions with check evasions and delta pruning, ordering
by TT move / MVV-LVA / killers. Eval: material + tapered piece-square
tables (Michniewski-style, file-symmetrised, packed as 32-char half
tables at 5cp steps) + bishop pair. Repetition vs the real game history
and the 50-move rule score 0 inside the search.

## Size

- main.py: 3,644 bytes (cap 4,096), packed from the readable source
  engine_source.py with the kit's official `makefun compress` tool.
  engine_source.py stays in this folder untracked, so it does not count
  toward the snapshot cap.
- Committed tree: 7,798 bytes (cap 8,192). LICENSE is the starter's MIT
  text.

## Practice results (kit env + scorer, run locally in process mode)

| version | instance | Elo | W/D/L | notes |
|---|---|---|---|---|
| v1: AB+TT+QS, 40k cap | trial-0 | 1279 | 2/2/2 | baseline is 1364 here |
| v2: +NMP/LMR/PVS, 60k cap | trial-0 | 1809 | 5/1/0 | |
| v2 | trial-1 | 1451 | 4/0/2 | baseline 1451 here |
| v3: v2 minus history heuristic (size golf) | trial-2 | 1191 | 2/1/3 | baseline 1279 here |
| final packed (v3 @ 80k cap) | trial-0 | 1809 | 5/1/0 | spare_plies 446 |

Practice floor is 1,154; house baseline (sunfish 4k) practice mean is
1,365. Per-instance Elo SD is ~115, so single trials are noisy; the
paired per-instance leads vs the baseline were +445 / 0 / -88.

Move generation verified by perft (6 standard suites, exact). Worst
single move ~2.8 s at the 80k cap on the dev VM; full local trial wall
~500 s of the 1,800 s budget (house time included locally).

## Pawn-structure experiment (reverted)

A pawn-structure eval term (doubled -6, isolated -8, passed-pawn bonus)
was tried in-session on top of the final build. It regressed practice
trial-0 to Elo 1451 from 1809, well beyond trial noise, so it was
reverted; only explanatory comments remain in engine_source.py. The
repacked final build was re-graded after the revert: Elo 1809,
W/D/L 5/1/0 on trial-0, unchanged.

## Next improvements

- SEE-based capture ordering is the cheapest gain left untried.
- The final also plays nodes-1M/2M (anchors 1667/1712): expect most
  points to come from the six practice rungs; protect the black games.
- More practice instances of the final build would sharpen the
  beat-the-baseline estimate (needs a mean lead whose 90% paired
  interval clears 0 over the 6 scored instances).

Dev tooling (perft tests, trial runner) lives outside this repo in
~/workspace/makefun-entries/dev/ so it does not count toward the
snapshot cap.

## Submission

Public repo: https://github.com/daffewd/tiny-chess-01 - the entry is
being prepared for submission.
