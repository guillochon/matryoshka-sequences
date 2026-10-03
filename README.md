# A124426 — Matryoshka dolls taken apart: nesting and stacking

How many ways can N Russian nesting dolls be arranged when each doll comes apart into a top half and a bottom
half? Four versions: pieces only nest, or can also stand on flat heads; and sizes are all distinct, or may repeat:

| Version | Counts for N = 1, 2, 3, … | OEIS |
|---|---|---|
| **Nesting** | 2, 10, 75, 780, 10556, 178031, … | [A124426](https://oeis.org/A124426), Bell(N)·Bell(N+1) (new interpretation, comment submitted) |
| **Stacking** (flat heads) | 2, 19, 312, 7643, 256020, 11096168, … | new sequence, submitted Oct 2026 |
| **Repeated sizes, nesting** | 2, 13, 117, 1485, 24508, 505381, … | new sequence |
| **Repeated sizes, stacking** | 2, 22, 415, 12160, 493998, 26174898, … | new sequence |

Every arrangement for N ≤ 4 is drawn on the project site: **https://guillochon.github.io/matryoshka-sequences/**

## Rules

Doll k is either closed (top on bottom) or split into its bottom B_k and top T_k.

- A piece only goes inside a piece of a *larger* doll.
- A closed doll or a bottom half holds at most one smaller closed doll or bottom half directly, which may hold
  another in turn. Nothing sits side by side inside a doll.
- A top half holds at most one smaller top half. A top never covers a doll or a bottom.
- A loose top never goes inside a bottom or a closed doll, even if its own bottom is in there too.
- Only what is inside (or on top of) what counts, not positions on the table.

**Stacking adds:** the flat head of a top half or of a closed doll can carry one smaller item (a closed doll, a
bottom or a top, with whatever it carries). A head shut inside a closed doll or under another top can't carry
anything. A piece standing in an open bottom sticks out, so its head is still usable.

## Results

**Nesting = Bell(N)·Bell(N+1).** The closed dolls and bottom halves form chains of nested pieces, and any grouping
of the N dolls into chains works: Bell(N) ways (this is Carlo Sanna's matryoshka comment in
[A000110](https://oeis.org/A000110)). Choosing which dolls are open and how their loose tops nest gives
Σ_k C(N,k)·Bell(k) = Bell(N+1).

**Stacking: no product formula.** Towers can alternate dolls, bottoms and tops (but a top can't sit inside an open
bottom), which ties the tops to everything else. Terms come from a recurrence: place pieces from largest to smallest
and track the free slots (heads at the top of a tower, empty open bottoms, chains inside outer closed dolls, chains
inside outer tops). With every doll closed the stacking count is [A000258](https://oeis.org/A000258)(N) =
Σ_k Stirling2(N,k)·Bell(k) (see [A008277](https://oeis.org/A008277)).

Stacking counts split by number of open dolls (rows sum to the sequence; column 0 is A000258):

| N \ open | 0 | 1 | 2 | 3 | 4 |
|---|---:|---:|---:|---:|---:|
| 1 | 1 | 1 | | | |
| 2 | 3 | 8 | 8 | | |
| 3 | 12 | 58 | 135 | 107 | |
| 4 | 60 | 446 | 1735 | 3221 | 2181 |

## Repeated sizes

N dolls whose sizes may repeat. Only the order of the sizes matters (1,1,3 = 1,1,2 = 2,2,3), so a size set is a
composition of N (how many dolls share each size, smallest first), and the count sums over all 2^(N−1) of them.
Dolls of equal size are identical (any top fits any bottom of its size) and never nest or stack on each other;
otherwise the nesting or stacking rules above apply. The all-distinct size set gives the sequences above; the
all-equal one gives N + 1.

Equal pieces make direct counting overcount symmetric arrangements, so `repeat_fast.py` uses Burnside's lemma:
an arrangement of identical pieces is an orbit of arrangements of labelled pieces under the permutations of equal
pieces. An arrangement fixed by a permutation g is a set of towers permuted by g; towers have no symmetries, so
the towers in an L-cycle are L copies of one tower built from L-cycles of pieces, and each non-base piece of such a
tower can be aligned with the copies in L ways. That reduces every fixed-point count to the slot recurrence of the
distinct-size case, run on the cycles of g, and one DP over sizes sums everything.

## Verification

| Count | Brute-force enumeration | Fast recurrence |
|---|---|---|
| Nesting | `nesting_brute.py`, N ≤ 6 | `nesting_fast.py`, asserts Bell(N)·Bell(N+1) for N ≤ 20 |
| Stacking | `stack_brute.py`, N ≤ 5 | `stack_fast.py` and the compact `prog.py`, N ≤ 60 |
| Repeated sizes (both) | `repeat_sizes.py`: canonical brute force N ≤ 4, Pólya multiset count N ≤ 6 | `repeat_fast.py` (Burnside) |

The JavaScript in the gallery pages runs its own enumeration in the browser and reproduces 2, 10, 75, 780;
2, 19, 312, 7643; 2, 13, 117, 1485; and 2, 22, 415, 12160.

## Files

| File | What it is |
|---|---|
| `code/nesting_brute.py`, `code/stack_brute.py` | enumerate every arrangement explicitly |
| `code/nesting_fast.py`, `code/stack_fast.py`, `code/prog.py` | slot-counting recurrences (`prog.py` is the OEIS PROG version) |
| `code/refine.py` | stacking counts split by number of open dolls |
| `code/pedestal.py` | checks a Dobinski-type identity a(N) = e⁻¹ Σ_m W_N(m)/m! (table replaced by m pedestals) |
| `code/repeat_sizes.py`, `code/repeat_fast.py` | repeated sizes: brute force and Pólya checks, Burnside counter |
| `data/stacking_n0-60.txt` | stacking count, n = 0..60 (b-file format) |
| `data/repeat_nesting.txt`, `data/repeat_stacking.txt` | repeated-size counts, one term per line from N = 1 |
| `nesting.html`, `stacking.html`, `repeat_nesting.html`, `repeat_stacking.html` | the galleries |
