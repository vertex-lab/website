---
title: Vertex FAQ
breadcrumbs: true
weight: 5
---

### What is Vertex?

Vertex is **Web of Trust as a Service** built on top of the nostr social graph. It provides reputation scores to offer personalized discovery and recommendations, as well as protection against impersonations and DoS attacks.

### How does Vertex work?

Vertex continuously processes profile events from the nostr network and applies ranking algorithms like Pagerank to determine reputation scores.

### How often do you update the trust scores?

Thanks to our advanced algorithms, we calculate the scores in real-time using the latest available data from a wide array of relays.

### Can I use Vertex to filter out spam?

Yes, our scores can help prioritize reputable users, power-up discovery and filter out low-quality content.

### Can I choose the algorithm?

Yes, we currently offer Global Pagerank and Personalized Pagerank, but more algorithm are going to be added in the future.

### What is the difference between Global and Personalized Pagerank?

[Global PageRank](https://en.wikipedia.org/wiki/PageRank) is an algorithm that assigns a score to each node in a graph, representing the "average perspective" based on the network's structure.

[Personalized PageRank](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8895775), on the other hand, tailors the results to an individual, adjusting rankings based on their unique connections and interactions within the graph.

### What do you mean by "global"?

On nostr there is no global state. However, our crawler gathers profile data from multiple relays to provide the most comprehensive view of the network. To maintain data quality and prevent spam attacks, we implement systems that filter out low-quality profiles and mitigate abuse.

### How can I integrate Vertex into my app?

We provide DVMs that make doing social graph analysis as simple as publishing an event.

Check out [our DVM specs](../nips) and hit our relay at `wss://relay.vertexlab.io` (request limits apply).

### Do I always need to connect to get a result?

DVMs return signed nostr events which can be cached by clients, shared between them, kept locally for offline usage and so on.

### Is Vertex open source?

Yes, we have proudly open sourced all of our code [here on our Github](https://github.com/vertex-lab).

We are committed to open standards, and that's why our services are structured as [NIP-90 Data Vending Machines](https://github.com/nostr-protocol/nips/blob/master/90.md). Browse [our DVM specs](../nips) to find the relevant links.

### Is Vertex trustless?

All Vertex responses are cryptographically signed. However, there is no trustless way to know that a response is legitimate except by running our code on the same data.

We are currently working on schemes that will allow you to (partially) verify our responses.

### Does Vertex store any personal data?

Vertex only processes publicly available data from the nostr network. It does not store user requests but does keep cached responses.

### Do you have any other questions?

Hit us up: [Vertex on nostr](https://njump.me/npub1kpt95rv4q3mcz8e4lamwtxq7men6jprf49l7asfac9lnv2gda0lqdknhmz)
