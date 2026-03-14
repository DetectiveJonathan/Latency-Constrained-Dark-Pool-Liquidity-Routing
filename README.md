# Latency-Constrained Dark Pool Liquidity Routing

> An advanced, time-expanded graph architecture for optimizing high-frequency liquidity routing across strictly constrained financial networks.

## Overview

In modern quantitative trading, routing massive block trades through a fragmented inter-dealer network of "dark pools" and Alternative Trading Systems (ATS) presents a complex topological challenge. Standard maximum flow algorithms (such as Edmonds-Karp or Dinic's) operate on static spatial graphs, rendering them blind to the temporal realities of financial markets.

This project implements a sophisticated mathematical reduction, transforming a dynamic, latency-constrained market topology into a strict 4-dimensional **Time-Expanded Graph (TEG)**. By applying vertex-splitting and temporal replication, the engine natively enforces real-world market microstructures—such as matching engine throughput bottlenecks and resting order book liquidity—allowing classical Max-Flow methodologies to flawlessly route liquidity within strict microsecond deadlines.

## Algorithmic Architecture

The engine bridges the gap between theoretical computer science and quantitative finance through three profound structural metamorphoses:

* **Temporal Replication:** The static 2D network $G = (V, E)$ is fractured across discrete microseconds up to an absolute latency bound $K$.
* **Vertex Splitting (Processing Limits):** To enforce regulatory and physical throughput caps $C_v$ on individual dark pools, every temporal node is split into an *in-node* and an *out-node*. An internal directed edge tightly bounds the maximum flow processed per microsecond.
* **Resting Liquidity:** "Wait edges" with infinite capacity seamlessly connect a node's present state to its future state, permitting shares to rest temporarily in a broker's ledger while waiting for outbound network bandwidth.

Because the resulting topology $G'$ is a massive Directed Acyclic Graph (DAG) with sparse connections, the architecture utilizes a **Dictionary-Backed Adjacency List**. This guarantees $O(1)$ edge-weight lookups while strictly allocating memory only for edges that physically exist, preventing the catastrophic cache misses associated with sparse $O(N^2 K^2)$ adjacency matrices.

## Complexity Analysis

Let $N = |V|$ and $M = |E|$ of the original static graph.

* **Space Complexity:** By utilizing the dictionary-backed adjacency list, memory allocation avoids quadratic penalties and remains tightly bounded to $\mathcal{O}(MK)$.
* **Time Complexity:** The Edmonds-Karp execution utilizes Breadth-First Search (BFS) to find the shortest augmenting paths. Substituting our specific topological bounds, the worst-case time complexity is $\mathcal{O}(N M^2 K^3)$. However, the practical runtime is drastically lower due to the strict temporal constraints ($K$) of high-frequency environments and the purely forward-flowing DAG structure.

## Installation & Usage

This engine requires no external dependencies beyond a standard Python 3 runtime environment.

1. Clone the repository:

```bash
git clone https://github.com/DetectiveJonathan/latency-constrained-routing.git
cd latency-constrained-routing

```

2. Execute the engine:

```bash
python3 router.py

```

### Input Formatting

The spatial connections of the market must be supplied via a plaintext file. Each line represents a directed connection between broker-dealers:

`u v capacity [latency]`

* `u`: Origin physical node ID.
* `v`: Destination physical node ID.
* `capacity`: Maximum volume of shares the connection can transmit in a single burst.
* `latency` (Optional): Traversal time in microseconds. Defaults strictly to 1.

### Example Execution

You can test the engine using the included asymmetric benchmark topology, which forces the algorithm to navigate competing constraints, spatial bottlenecks, and temporal convergence.

**benchmark.txt**

```text
# Source to Initial Brokers
0 1 60 1
0 2 50 2

# Inter-Dealer Routing
1 3 40 1
1 5 25 3
2 3 30 1
2 4 40 2

# Dark Pool to Market (Sink)
3 4 60 1
4 5 80 1

```

When prompted by the CLI, enter the following systemic constraints:

* **N:** `6`
* **K:** `5`
* **Source:** `0`
* **Sink:** `5`
* **Default Processing Capacity:** `40`

The engine will successfully orchestrate multi-wave continuous flow and temporal collision avoidance to return a maximum routeable liquidity of **105 shares**.

---

**Author:** Tianchi He

---
