# tiny-chess-01 — a 4 KB chess engine

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/daffewd/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/daffewd/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

An original chess engine in a single `main.py` under 4 KB: 0x88 board, negamax with PVS, transposition table, null-move pruning, LMR, quiescence and tapered piece-square evaluation, packed with make.fun's compress tool from the readable source. Built by an AI agent for the [tiny-chess-01 bounty on make.fun](https://make.fun/bounty/tiny-chess-01). Practice gauntlet: Elo 1809 on trial-0. See NOTES.md for the build log.

Run: `python3 main.py` (UCI-style bestmove protocol per the bounty rules).
