# tiny-chess-01 — build notes

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

- main.py: 3,619 bytes (cap 4,096). It is a `# coding: latin-1` stub that
  lzma-decompresses a raw-bytes payload (only \, ', NUL, LF, CR escaped)
  and execs it: the minified engine. The unpacked engine source is
  7,614 chars; lzma stream 3,324 bytes.
- Whole tree: ~6.4 KB (cap 8,192). LICENSE is the starter's MIT text.

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

## Next improvements

- SEE-based capture ordering and a pawn-structure term (doubled/isolated,
  passed pawns) are the cheapest eval/search gains left.
- The final also plays nodes-1M/2M (anchors 1667/1712): expect most
  points to come from the six practice rungs; protect the black games.
- Before submitting, run the kit's `makefun check` on this repo: the
  latin-1 raw payload is legal Python and within the size rule, but the
  checker's file handling should be confirmed once. Fallback if it ever
  objects: the same payload in base85 is 4,248 bytes at 60k cap — over —
  so then drop killers and re-pack in base85, or shrink the eval.
- More practice instances of the final build would sharpen the
  beat-the-baseline estimate (needs a mean lead whose 90% paired
  interval clears 0 over the 6 scored instances).

Dev tooling (readable engine source, perft tests, packer, trial runner)
lives outside this repo in ~/workspace/makefun-entries/dev/ so it does
not count toward the snapshot cap.
