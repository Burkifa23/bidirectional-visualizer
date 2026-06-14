#  Bidirectional Heuristic Search Visualizer

This document provides a detailed technical explanation of **Bidirectional Heuristic Search** as implemented in **`bidirectional-visualizer.html`** for solving the Ghana Route-Finding Problem.

---

##  What is Bidirectional Search?

**Bidirectional Search** is a search strategy that runs two simultaneous searches:

1. **Forward Search**: Starts from the **Start Node** and moves towards the Goal Node.
2. **Backward Search**: Starts from the **Goal Node** and moves back towards the Start Node (traversing edges in reverse, if the graph is directed).

By utilizing heuristics for both search directions, this visualizer implements **Bidirectional A* Search**. The search terminates when the two search frontiers **intersect** (meaning they have both expanded a common node), which can significantly reduce the overall number of nodes explored.

---

## Step-by-Step Algorithm Mechanics

The bidirectional search alternates steps between the two directions:

1. **Frontier Initialization**:
   * Forward Frontier (F_f): Initialized with the start city (g = 0, f = h(start, goal)).
   * Backward Frontier (F_b): Initialized with the destination city (g = 0, f = h(goal, start)).
2. **Step Cycles**:
   * **Forward Step**:
     * Pop the node n with the lowest f-value from F_f. Add n to the forward explored set.
     * **Intersection Check**: If n is already present in the backward explored set, a collision has occurred! Stop search and reconstruct the complete path.
     * Expand n's neighbors. For each unvisited neighbor, add to F_f sorted by:
       \[f_f(c) = g_f(c) + h(c, goal)\]
   * **Backward Step**:
     * Pop the node m with the lowest f-value from F_b. Add m to the backward explored set.
     * **Intersection Check**: If m is already present in the forward explored set, a collision has occurred! Stop search and reconstruct the complete path.
     * Expand m's neighbors. For each unvisited neighbor, add to F_b sorted by:
       \[f_b(c) = g_b(c) + h(c, start)\]
3. **Path Reconstruction**: Combine the path traversed by the forward search from the start node to the collision node with the reversed path traversed by the backward search from the goal node to the collision node.

---

## Visualizing the Ghana Road Network with Bidirectional A*

In the visualizer, you can select a **Start City** (e.g., **Tamale**) and a **Destination City** (e.g., **Korle Bu**).

Here is a conceptual trace of the search:
* **Forward Frontier (Blue)**: Starts expanding outward from **Tamale** \to Kintampo \to Techiman \to Kumasi.
* **Backward Frontier (Orange)**: Starts expanding backward from **Korle Bu** \to Accra \to Koforidua \to Kumasi.
* **Intersection**: Both frontiers intersect at **Kumasi**. The forward search has Kumasi in its explored set, and the backward search expands Kumasi as well.
* **Result**: The visualizer instantly flags Kumasi as the **Meeting Node (Emerald)**, constructs the final path (Tamale \to Kintampo \to Techiman \to Kumasi \to Koforidua \to Accra \to Korle Bu), and terminates.

---

##  Complexity Analysis

The primary mathematical benefit of bidirectional search is the reduction in search depth.

### 1. Time Complexity
Instead of exploring a single search bubble of depth d which expands O(b^d) nodes, bidirectional search explores two smaller search bubbles of depth d/2:

\[O(b^{d/2} + b^{d/2}) = O(b^{d/2})\]
where:
* b: average branching factor (\approx 3 to 5 in Ghana).
* d: depth of the optimal path.

This represents an exponential reduction in the number of node evaluations. For example, if b = 10 and d = 6, a single-direction search might evaluate 10^6 = 1,000,000 nodes, whereas bidirectional search evaluates only 2 \cdot 10^3 = 2,000 nodes.

### 2. Space Complexity
Since we must store both frontiers and explored maps in memory to detect intersections:

\[O(b^{d/2})\]
This is a significant improvement over the O(b^d) space complexity of standard single-direction A* Search.

---

## 🎨 Visualization State System

The visualizer shows the active execution states of Bidirectional A* in real-time:
* 🔘 **Slate Gray (`#cbd5e1`)** — Unvisited cities.
* 🔵 **Soft Blue (`#74ade8`)** — Cities expanded by the Forward search frontier.
* 🟡 **Amber Gold (`#d97706`)** — Cities expanded by the Backward search frontier.
* 🟢 **Emerald Green (`#10b981`)** — The collision meeting node and the final combined path.
* **Frontiers Panel** — Displays the top elements currently queued in both the Forward and Backward priority queues.
