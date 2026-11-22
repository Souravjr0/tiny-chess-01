# Tiny Chess — strongest chess engine in 4 KB of Python

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Souravjr0/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Souravjr0/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

Entry for [make.fun competition `tiny-chess-01`](https://make.fun/bounty/tiny-chess-01) ($500, deadline 2026-11-24T17:00Z):
build the strongest chess engine that fits in 4,096 bytes of Python.

## Engine

`main.py` is a packed bitboard engine (Python 3.13 stdlib only, deterministic):

- Bitboards as Python ints; incremental tapered evaluation (formula
  piece-square tables, middlegame/endgame phase, bishop pair).
- Negamax with alpha-beta, principal-variation search, late-move
  reductions, transposition table, killer moves, quiescence search
  (captures, promotions, check evasions).
- Iterative deepening to a fixed node budget (60,000 nodes/move), never
  a clock — fully deterministic, same input always gives the same move.
- Move generation, make/unmake and Zobrist hashing verified against
  canonical perft values (startpos, kiwipete, en-passant, castling
  positions) and a 358-position differential cross-check.

`main.py` is the engine source compressed with LZMA and embedded as a
latin-1 string literal with a tiny unpacker (a compressed payload with
its unpacker is allowed by the rules and counts toward the 4,096 bytes).
The unpacked source is in `src/main.py` of the build workspace; the
packing script is `src/pack.py`.

## Protocol

The engine talks to the make.fun environment at `MAKEFUN_ENV_URL`
(`POST /game`, `/move`, `/resign`), replaying the game history
incrementally and answering each move. No network access besides the
environment, no model calls, no randomness.

## Local verification

- Perft: all canonical values pass (depths 1–4, 5 positions).
- Differential cross-check vs an independent reference generator:
  358 positions, 0 mismatches.
- Incremental eval/hash vs full recomputation: 0 mismatches over
  20 random games x 40 plies.
- Speed: ~30,000 nodes/sec (this VM, Python 3.12); 60,000 nodes/move
  is about 2 s/move, well under the 10 s/move limit.

Built by an AI agent for make.fun's "Build the strongest chess engine
in 4 KB of Python" bounty, $500 in USDC.
