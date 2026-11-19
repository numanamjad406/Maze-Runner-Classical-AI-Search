<div align="center">

# 🧩 The Maze Runner

### Classical AI Search Algorithms in Python

Guide a runner out of the maze with **DFS, BFS, DLS, IDS, UCS, Greedy Best-First and A\***, then make it harder: collect every hidden code piece *before* escaping.

![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)
![Tkinter](https://img.shields.io/badge/GUI-Tkinter-2b7bc4)
![Topic](https://img.shields.io/badge/Topic-Classical%20AI%20Search-8e2c38)
![Course](https://img.shields.io/badge/ITU-BS%20Artificial%20Intelligence-e0a526)

</div>

![A* solving the key hunt](screenshots/04_astar_key_hunt.png)

---

## 📌 Table of Contents

- [About](#-about)
- [Features](#-features)
- [Screenshots](#-screenshots)
- [Algorithms](#-algorithms)
- [Heuristics](#-heuristics)
- [Problems](#-problems)
- [Terrain Costs](#-terrain-costs)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
- [Results](#-results)
- [Concepts Applied](#-concepts-applied)
- [What I Learned](#-what-i-learned)
- [Author](#-author)

---

## ✨ About

The runner starts at the Glade door (`S`) and must reach the exit (`E`) while avoiding walls and paying different costs for different terrain. Every algorithm talks to the maze only through a small `SearchProblem` interface, so the **same search code** solves plain mazes, test graphs and the key-hunting problem.

Algorithms are compared on:

- **Path cost** (is it optimal?)
- **Steps**
- **Nodes expanded**
- **Search time**

## 🚀 Features

- 7 search algorithms, all built on shared fringe containers (`Stack`, `Queue`, `PriorityQueue`)
- Animated **Tkinter** display: heat-map of expansion order, then the runner follows the path
- **Text display** for terminals and SSH sessions
- Weighted terrain (ivy and Griever territory)
- Key-collection problem with a **consistent, MST-based A\* heuristic**
- `--compare` mode that prints a table of every algorithm
- Timeouts so slow searches (such as IDS on big mazes) don't hang the program

## 📸 Screenshots

| BFS on a 23×41 maze | A\* with terrain costs |
|:---:|:---:|
| ![BFS](screenshots/01_bfs_maze_medium.png) | ![A* Griever Alley](screenshots/02_astar_griever_alley.png) |
| Explores level by level (372 nodes). | Routes around costly `G` cells (205 nodes). |

| A\* on the Greedy Trap | Algorithm comparison |
|:---:|:---:|
| ![A* Greedy Trap](screenshots/03_astar_greedy_trap.png) | ![Compare](screenshots/05_compare_table.png) |
| Optimal cost 59, where greedy search returns 84. | Output of `--compare`. |

> Colour guide: warm cells are expanded states (yellow = early, red = late), the blue line is the final path, and diamonds are code pieces.

## 🧠 Algorithms

| Algorithm | Category | Fringe | Optimal? |
|---|---|---|---|
| Depth-First Search (DFS) | Uninformed | Stack | No |
| Breadth-First Search (BFS) | Uninformed | Queue | Fewest steps only |
| Depth-Limited Search (DLS) | Uninformed | Stack + depth limit | No |
| Iterative Deepening Search (IDS) | Uninformed | Repeated DLS | Fewest steps only |
| Uniform-Cost Search (UCS) | Uninformed | Priority queue on `g(n)` | **Yes** |
| Greedy Best-First Search (GBFS) | Informed | Priority queue on `h(n)` | No |
| A\* Search | Informed | Priority queue on `g(n) + h(n)` | **Yes** (admissible `h`) |

Implementation notes:

- DFS, BFS, UCS, GBFS and A\* share one `generalGraphSearch` routine. Only the fringe and the priority function change.
- The goal test happens when a state is **popped**, and a state is never expanded twice (graph search).
- DLS returns a path, `CUTOFF` (the limit was hit, so a deeper solution may exist) or `None` (provably no solution). It avoids cycles by checking the current path only, not a global explored set, so it can't miss a solution that fits within the limit.

## 📐 Heuristics

| Heuristic | Used for | Idea |
|---|---|---|
| **Manhattan** | Maze | `\|r1 - r2\| + \|c1 - c2\|` |
| **Euclidean** | Maze | `√((r1 - r2)² + (c1 - c2)²)` |
| **Key Hunt** | Key hunt | `max(h1, h2)` using true maze distances (see below) |

**Key Hunt heuristic.** Both parts are lower bounds on the remaining cost, so their maximum is also a lower bound:

1. **Farthest key.** Go from the runner to the most expensive key and then to the exit.
2. **Minimum spanning tree.** Build an MST over the runner, the remaining keys and the exit, using real maze distances. Any tour that visits all of these must be at least this long.

Distances come from `mazeDistance` (a cached BFS), so the heuristic stays admissible and consistent while being far more informed than Manhattan distance.

## 🎯 Problems

**Maze problem.** The state is `(row, col)`. Reach `E`.

**Key hunt problem.** The state is `(position, remainingKeys)`, where `remainingKeys` is a `frozenset`. A key is removed from the set automatically when the runner steps on it. The goal is "no keys left **and** standing on `E`". The state stays small because it never stores the maze itself.

## 🌿 Terrain Costs

The cost is paid when you **enter** a cell.

| Symbol | Meaning | Cost |
|:---:|---|:---:|
| `%` | Wall | impassable |
| ` ` / `.` | Corridor | 1 |
| `S` | Start (Glade door) | 1 |
| `E` | Exit (Griever Hole) | 1 |
| `K` | Code piece | 1 |
| `~` | Thick ivy | 3 |
| `G` | Griever territory | 10 |

## 📁 Project Structure

```text
Maze-Runner-Classical-AI-Search/
├── runner.py        # command-line entry point, comparison table, timeouts
├── search.py        # DFS, BFS, DLS, IDS, UCS, GBFS, A*
├── problems.py      # SearchProblem, MazeProblem, GraphProblem, mazeDistance
├── griever_hole.py  # KeyHuntProblem + key hunt heuristic
├── heuristics.py    # null, Manhattan, Euclidean heuristics
├── maze.py          # maze loader and terrain rules
├── display.py       # text and Tkinter displays
├── util.py          # Stack, Queue, PriorityQueue
├── screenshots/     # images used in this README
└── mazes/           # *.maze layouts
```

## ⚙️ Getting Started

**Requirements:** Python 3.8+ and Tkinter (bundled with most Python installs; on Ubuntu run `sudo apt install python3-tk`). No other packages are needed.

```bash
git clone https://github.com/numanamjad406/Maze-Runner-Classical-AI-Search.git
cd Maze-Runner-Classical-AI-Search
python runner.py
```

## ▶️ Usage

```bash
python runner.py -l <maze> -a <algorithm> [-H <heuristic>] [-p maze|keys] [-t] [-d <limit>]
```

| Flag | Meaning |
|---|---|
| `-l` | maze name or path (default `maze_tiny`) |
| `-a` | `dfs`, `bfs`, `dls`, `ids`, `ucs`, `gbfs`, `astar` |
| `-p` | `maze` (reach the exit) or `keys` (collect all pieces, then exit) |
| `-H` | `manhattan`, `euclidean`, `null`, `keyhunt` |
| `-d` | depth limit for DLS |
| `-t` | text output instead of a window |
| `-q` | print statistics only |
| `-z`, `-f` | window zoom, seconds per animation step |
| `--compare` | run every algorithm and print a table |
| `--timeout` | give up after N seconds |
| `--list` | list available mazes |

**Examples**

```bash
python runner.py -l maze_small -a bfs                       # BFS with animation
python runner.py -l maze_small -a dfs -t                    # DFS, text output
python runner.py -l griever_alley -a ucs                    # terrain-aware optimal search
python runner.py -l greedy_trap -a astar -H manhattan       # A* vs. the greedy trap
python runner.py -l maze_small -a dls -d 40                 # depth-limited search
python runner.py -l keys_medium -p keys -a astar -H keyhunt # collect keys, then escape
python runner.py -l maze_medium --compare                   # compare all algorithms
```

While the window is open, press **Space** to skip the animation and **Q / Esc** to close it.

## 📊 Results

`python runner.py -l maze_medium --compare` (23×41 maze):

| Algorithm | Path cost | Steps | Nodes expanded |
|---|---:|---:|---:|
| DFS | 130 | 130 | 157 |
| BFS | 80 | 80 | 372 |
| IDS | 80 | 80 | 987,790 |
| UCS | 80 | 80 | 372 |
| Greedy [Manhattan] | 82 | 82 | **87** |
| **A\*** [Manhattan] | **80** | 80 | 198 |

Terrain maze (`griever_alley`), where steps and cost differ:

| Algorithm | Path cost | Steps | Nodes expanded |
|---|---:|---:|---:|
| BFS | 91 | 46 | 266 |
| UCS | **78** | 46 | 255 |
| Greedy [Manhattan] | 83 | 58 | 58 |
| **A\*** [Manhattan] | **78** | 46 | 205 |

Key hunt (`keys_medium`, 4 code pieces):

| Algorithm | Path cost | Nodes expanded |
|---|---:|---:|
| DFS | 362 | 583 |
| BFS / UCS | 84 | 1,740 |
| **A\*** [keyhunt] | **84** | **86** |

**Takeaways**

- BFS is optimal only when every step costs the same. UCS and A\* stay optimal when terrain costs differ.
- Greedy search is fast but can return a costly path because it ignores `g(n)`.
- A\* with the key hunt heuristic expands **about 20× fewer states than BFS/UCS** (86 vs. 1,740) with the same optimal cost.
- IDS re-expands nodes at every depth, so it is very expensive on larger mazes.

> The numbers above were produced on the demo mazes in `mazes/`. Run `--compare` on your own layouts to get your own results.

## 📚 Concepts Applied

State-space representation · graph vs. tree search · stacks, queues and priority queues · uninformed vs. informed search · admissible and consistent heuristics · A\* · depth-limited and iterative-deepening search · problem formulation

## 🎓 What I Learned

- Implementing classical search algorithms and how their fringe choice changes behaviour
- Designing compact, hashable state representations for harder problems
- Building admissible and consistent heuristics (MST over true maze distances)
- Evaluating algorithms by cost, expansions and time rather than guesswork

## 👨‍💻 Author

**Numan Amjad**
🎓 BS Artificial Intelligence, Information Technology University (ITU), Lahore
🔗 [github.com/numanamjad406](https://github.com/numanamjad406)

---

⭐ If you found this project useful, consider giving it a star!
