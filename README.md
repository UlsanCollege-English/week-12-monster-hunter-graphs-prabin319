[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/80z-ZS6n)
# Week 12: Monster Hunter Graphs

## Student

Name: KHADKA PRABIN

Student ID: 2412091

## Summary

This assignment builds a monster-activity graph for the Hunter Guild. Each **location** (Old Theater, Train Station, etc.) is a **node**, and each **route** between two locations is an **undirected edge**. The weighted variant adds a danger score to each edge. I implemented five functions: building an unweighted adjacency list, building a weighted adjacency dict, summarising graph size, finding the most-connected hub, and ordering sighting reports by urgency using a min-heap. The hardest function was `build_weighted_hunter_map` because it required validating weights, handling duplicate edges, and keeping only the lowest danger score across both directions simultaneously.

## Approach

- `build_hunter_map`: Iterate over every `(a, b)` pair. Ensure both `a` and `b` exist as keys, then append the opposite endpoint only when it is not already in the neighbor list, preventing duplicates. Both directions are added in one pass.
- `build_weighted_hunter_map`: Same two-key setup. Validate that `weight > 0` (raise `ValueError` otherwise). For each direction, only write the weight if the key is new or the incoming weight is strictly lower — this implements the "keep lowest" rule in O(1) per edge per direction.
- `map_summary`: `len(graph)` gives the location count. Summing all neighbor-list lengths gives the total directed-edge count; dividing by 2 gives the undirected route count (valid because the graph is always symmetric).
- `most_connected_location`: Use `min()` with a key of `(-degree, name)`. Negating the degree turns "maximum degree" into a `min` search; the string name breaks ties lexicographically.
- `priority_hunt_order`: Push the `(priority, location)` tuples directly into a list and call `heapq.heapify`. Python tuples compare element-by-element, so ties on priority are automatically broken alphabetically by location name. Pop until empty.

## Complexity

### `build_hunter_map`

- Time: O(E) where E = number of edges
- Space: O(V + E) for the adjacency list
- Why: One pass over edges; each edge adds at most two list entries. The duplicate check is O(degree) per node per edge in the worst case, but for typical sparse graphs this is effectively O(E).

### `build_weighted_hunter_map`

- Time: O(E)
- Space: O(V + E) for the nested dict
- Why: One pass over edges with O(1) dict lookups and comparisons per edge.

### `map_summary`

- Time: O(V + E) — must visit every node and sum their neighbor counts
- Space: O(1) — only two counters stored beyond the input
- Why: A single `sum()` over all neighbor lists touches every edge entry once.

### `most_connected_location`

- Time: O(V) — `min()` scans every key once
- Space: O(1) — no extra data structures
- Why: Linear scan with a two-element comparison key; no sorting needed.

### `priority_hunt_order`

- Time: O(E log E) for heapify + E pops, where E = number of reports
- Space: O(E) for the heap
- Why: `heapq.heapify` is O(n); each `heappop` is O(log n); done n times = O(n log n) total.

## Edge-Case Checklist

- [x] Empty graph
- [x] One route
- [x] Duplicate routes
- [x] Disconnected locations
- [x] Tie for most connected location
- [x] Positive weighted routes
- [x] Invalid zero or negative danger score
- [x] Empty priority report list

## Tests

```bash
pytest -q
```

Result:

```text
................
16 passed in 0.04s
```

## Assistance & Sources

AI used? Yes

If yes, what did it help with?

- Helped structure and implement all five functions, and complete this README with complexity analysis.

Other sources used:

- Python `heapq` docs: https://docs.python.org/3/library/heapq.html
- HOMEWORK_BRIEF.md provided by instructor