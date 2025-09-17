---
title: FAQs
breadcrumbs: true
weight: 3
---

{{% details title="What is Vertex?" %}}

Vertex is Web of Trust as a Service built on top of the nostr social graph. It provides reputation scores to offer personalized discovery and recommendations, as well as protection against impersonations and spam.

{{% /details %}}

{{% details title="How can I integrate Vertex into my app?" closed="true"%}}

We provide DVMs that make doing social graph analysis as simple as publishing an event. Check out [our services](../services/) and hit our relay at:

`wss://relay.vertexlab.io`

 If you need help, don't hesitate to [ask Pip](https://signal.me/#eu/O6mL1ozXhujHr-seJPkZzA4EKprDgr64AqoAOhu4U5TtKlRwutNJvbsCqXJvZb1-).

{{% /details %}}

{{% details title="How does Vertex work?" closed="true" %}}

Vertex continuously processes profile events from the nostr network and applies ranking algorithms like Pagerank to determine reputation scores.

{{% /details %}}

{{% details title="How often do you update the reputational scores?" closed="true"%}}

Thanks to our advanced algorithms, we calculate the scores in real-time using the latest available data from a wide array of relays.

{{% /details %}}


{{% details title="Can I use Vertex to filter out spam?" closed="true"%}}

Yes, our services can help you prioritize reputable users, power-up discovery and filter out low-quality content. Learn how [here](../services/)

{{% /details %}}

{{% details title="Can I choose the algorithm?" closed="true"%}}

Yes, you simply need to specify it in the `sort` param of the request. We currently support `followerCount`, `globalPagerank`, and `personalizedPagerank`.  

To learn more, check out our [algorithms](/docs/algos).

{{% /details %}}


{{% details title="What do you mean by \"global\"" closed="true"%}}

On nostr there is no global state. However, our crawler gathers profile data from multiple relays to provide the most comprehensive view of the network. To maintain data quality and prevent spam attacks, we implement systems that filter out low-quality profiles and mitigate abuse.

{{% /details %}}

{{% details title="Do I always need to connect to get a result?" closed="true"%}}

DVMs return signed nostr events that can be cached by clients, shared between them, kept locally for offline usage and so on.

{{% /details %}}

{{% details title="Is Vertex open source?" closed="true"%}}

Yes, we have proudly open sourced all of our code [here on our Github](https://github.com/vertex-lab).

We are committed to open standards, and that's why our services are structured as [NIP-90 Data Vending Machines](https://github.com/nostr-protocol/nips/blob/master/90.md).

{{% /details %}}

{{% details title="Is Vertex trustless?" closed="true"%}}

All Vertex responses are cryptographically signed. However, there is no trustless way to know that a response is legitimate except by running our code on the same data.

We are currently building client-side validation schemes so your users will be able to verify independently.

{{% /details %}}

{{% details title="Does Vertex store any personal data?" closed="true"%}}

Vertex only processes publicly available data from the Nostr network. We respect user privacy and will honor any legitimate requests for content removal.

{{% /details %}}

<a id="credits"></a>
{{% details title="How many credits do I have left?" closed="false"%}}

Connect to our relay at `wss://relay.vertexlab.io` and authenticate using NIP-42.  

Then request `kind:22243` to get your credit balance.

```json
{
    "kind": 22243,
    "tags": [
        ["credits", "9847"],
        ["lastRequest", "1746466043"]
    ],
    // ...
}
```

{{% /details %}}

{{% details title="Do you have any other questions?" closed="true"%}}

Just [ask Pip](https://npub.world/npub176p7sup477k5738qhxx0hk2n0cty2k5je5uvalzvkvwmw4tltmeqw7vgup)

{{% /details %}}
