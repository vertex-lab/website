---
title: Algorithms
breadcrumbs: true
weight: 15
---

All of our services accept an optional sort parameter, that allows control over ranking behavior to optimize for performance, robustness, or personalization.
These are the sorting algorithms currently supported

- `followerCount`:  
A simple algorithm that ranks entities solely by their number of followers.

- `globalPagerank`:  
A slight modification of [Pagerank](https://en.wikipedia.org/wiki/PageRank), which assigns each node a score representing its "average perceived importance" based on the overall structure of the network.

- `personalizedPagerank`:  
A variation of [Personalized Pagerank](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8895775), which adjusts rankings for each user based on their unique connections and interactions within the graph.

## Tradeoffs

| Algorithm | Speed | Cost | Integrity | View |
| --- | --- | --- | --- | --- |
| `followerCount` | Fast <br> ~50ms | 1   | Medium | global |
| `globalPagerank` | Fast <br> ~50ms | 1   | High | global |
| `personalizedPagerank` | Slower <br> ~600ms | 10  | Highest | personal |
  
- **Cost**:  
The cost for each request, measured in credits. For the $ prices, [check out our pricing](/pricing).
  
- **Integrity**:  
How resistant the algorithm is to manipulation. High integrity indicate that it's hard for attackers to artificially boost their ranking.
  
- **View:**  
Indicates whether the ranking is global—the same for everyone—or personalized based on the provided `source`.