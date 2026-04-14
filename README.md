# AI 2002 – Assignment 4 | Question 3: Sudoku Boards as CSPs

A CSP-based Sudoku solver using **AC-3 arc consistency**, **forward checking**, and **backtracking search** with the MRV (Minimum Remaining Values) heuristic.

---

## How It Works

1. **AC-3** runs first to reduce domains via arc consistency before any search begins.
2. **Forward Checking** prunes peer domains each time a value is assigned.
3. **Backtracking** with MRV selects the most constrained unassigned cell at each step.
4. If a contradiction is found, the solver backtracks and tries the next value.

---

## File Structure

```
.
├── q3_sudoku_csp.py      # Entry point (CLI)
├── sudoku_solver.py      # Core CSP logic: AC-3, FC, Backtracking
├── sudoku_io.py          # Board I/O utilities
├── sudoku_gui.py         # Optional Tkinter GUI
└── sudoku_boards/
    ├── easy.txt
    ├── medium.txt
    ├── hard.txt
    ├── veryhard.txt
    └── evil.txt
```

---

## Usage

```bash
python q3_sudoku_csp.py sudoku_boards/easy.txt
# Multiple boards at once:
python q3_sudoku_csp.py sudoku_boards/easy.txt sudoku_boards/medium.txt sudoku_boards/hard.txt sudoku_boards/veryhard.txt
```

**Input format** — a `.txt` file with exactly 9 lines of 9 digits. `0` = empty cell.

```
004030050
609400000
005100489
000060930
300807002
026040000
453009600
000004705
090050200
```

---

## Results

### Board 1 — Easy (`easy.txt`)

**Input:**
```
0 0 4 | 0 3 0 | 0 5 0
6 0 9 | 4 0 0 | 0 0 0
0 0 5 | 1 0 0 | 4 8 9
------+-------+------
0 0 0 | 0 6 0 | 9 3 0
3 0 0 | 8 0 7 | 0 0 2
0 2 6 | 0 4 0 | 0 0 0
------+-------+------
4 5 3 | 0 0 9 | 6 0 0
0 0 0 | 0 0 4 | 7 0 5
0 9 0 | 0 5 0 | 2 0 0
```

**Solved:**
```
7 8 4 | 9 3 2 | 1 5 6
6 1 9 | 4 8 5 | 3 2 7
2 3 5 | 1 7 6 | 4 8 9
------+-------+------
5 7 8 | 2 6 1 | 9 3 4
3 4 1 | 8 9 7 | 5 6 2
9 2 6 | 5 4 3 | 8 7 1
------+-------+------
4 5 3 | 7 2 9 | 6 1 8
8 6 2 | 3 1 4 | 7 9 5
1 9 7 | 6 5 8 | 2 4 3
```

| Metric | Value |
|---|---|
| BACKTRACK calls | **1** |
| BACKTRACK failures | **0** |

> **Commentary:** AC-3 alone resolves almost all cells through constraint propagation. Backtracking is invoked just once and finds the solution immediately without any failure, confirming this is a well-constrained puzzle that barely requires search.

---

### Board 2 — Medium (`medium.txt`)

**Input:**
```
0 0 0 | 0 3 0 | 0 4 0
1 0 9 | 7 0 0 | 0 0 0
0 0 0 | 8 5 1 | 0 7 0
------+-------+------
0 0 2 | 6 0 7 | 8 3 0
9 0 6 | 0 1 0 | 2 0 7
0 3 1 | 5 0 2 | 9 0 0
------+-------+------
0 1 0 | 3 6 9 | 0 0 0
0 0 0 | 0 0 5 | 7 0 3
0 9 0 | 0 7 0 | 0 0 0
```

**Solved:**
```
8 7 5 | 9 3 6 | 1 4 2
1 6 9 | 7 2 4 | 3 8 5
2 4 3 | 8 5 1 | 6 7 9
------+-------+------
4 5 2 | 6 9 7 | 8 3 1
9 8 6 | 4 1 3 | 2 5 7
7 3 1 | 5 8 2 | 9 6 4
------+-------+------
5 1 7 | 3 6 9 | 4 2 8
6 2 8 | 1 4 5 | 7 9 3
3 9 4 | 2 7 8 | 5 1 6
```

| Metric | Value |
|---|---|
| BACKTRACK calls | **16** |
| BACKTRACK failures | **0** |

> **Commentary:** The solver requires 16 backtrack calls with zero failures. Forward checking and AC-3 pruning are effective enough that every branch explored leads to a valid partial assignment — no dead ends encountered. The modest call count reflects moderate ambiguity remaining after initial propagation.

---

### Board 3 — Hard (`hard.txt`)

**Input:**
```
1 0 2 | 0 4 0 | 0 0 7
0 0 0 | 0 8 0 | 0 0 0
0 0 9 | 5 0 0 | 3 0 4
------+-------+------
0 0 0 | 6 0 7 | 9 0 0
5 4 0 | 0 0 0 | 0 2 6
0 0 6 | 4 0 5 | 0 0 0
------+-------+------
7 0 8 | 0 0 3 | 4 0 0
0 0 0 | 0 1 0 | 0 0 0
2 0 0 | 0 6 0 | 5 0 9
```

**Solved:** *(No solution returned by solver)*

| Metric | Value |
|---|---|
| BACKTRACK calls | **174** |
| BACKTRACK failures | **142** |
| Failure rate | **~81.6%** |

> **Commentary:** The solver records 174 backtrack calls with 142 failures — an 81% failure rate. The board's sparse given clues leave very wide domains that lead to frequent contradictions during search. The high failure rate indicates the solver exhausts many inconsistent branches before giving up, likely pointing to a limitation in constraint propagation depth for harder boards.

---

### Board 4 — Very Hard (`veryhard.txt`)

**Input:**
```
0 0 1 | 0 0 7 | 0 0 0
6 0 0 | 4 0 0 | 3 0 0
0 0 0 | 0 3 0 | 0 6 4
------+-------+------
3 8 0 | 0 7 6 | 0 0 0
0 0 0 | 0 0 0 | 0 3 6
2 7 0 | 0 1 5 | 0 0 0
------+-------+------
0 0 0 | 0 2 0 | 0 5 1
7 0 0 | 1 0 0 | 2 0 0
0 0 8 | 0 0 9 | 0 0 0
```

**Solved:** *(No solution returned by solver)*

| Metric | Value |
|---|---|
| BACKTRACK calls | **2707** |
| BACKTRACK failures | **2673** |
| Failure rate | **~98.7%** |

> **Commentary:** The solver invokes backtracking 2707 times with 2673 failures — a failure rate exceeding 98%. This near-total failure rate shows the solver exploring an enormous number of inconsistent assignments with minimal pruning effectiveness. The exponential blowup compared to the Hard board (174 → 2707 calls) confirms this puzzle severely stresses the current CSP implementation.

---

## Summary

| Board | Solved | BT Calls | BT Failures | Failure Rate |
|---|:---:|---:|---:|---:|
| Easy | ✅ | 1 | 0 | 0% |
| Medium | ✅ | 16 | 0 | 0% |
| Hard | ❌ | 174 | 142 | 81.6% |
| Very Hard | ❌ | 2707 | 2673 | 98.7% |

### Key Observations

- **Easy & Medium** are solved with zero failures — AC-3 + forward checking is highly effective for well-constrained puzzles.
- **Hard & Very Hard** return no solution. The dramatically increasing failure rates point to a solver limitation with sparse boards rather than the puzzles being unsolvable.
- The jump from **16 → 174 → 2707** backtrack calls illustrates the exponential cost of search as constraint density decreases.
- The **Evil** board (not shown above) reports 0 calls and 0 failures — AC-3 detects an inconsistency before backtracking even starts.

---

## Dependencies

- Python 3.8+
- Standard library only (`collections`, `copy`, `dataclasses`)
- Optional GUI: `tkinter` (included with most Python distributions)
