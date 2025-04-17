---
title: Why We Don't Use NIP-85
date: 2025-04-08
summary: "When it comes to the Nostr Web of Trust, several NIPs have been proposed, but the one that seemed to gather the most traction was Trusted Assertion by Vitor Pamplona..."
---

When it comes to the Nostr Web of Trust, several NIPs have been proposed, but the one that seemed to gather the most traction was [Trusted Assertion](https://github.com/nostr-protocol/nips/pull/1534) by [Vitor Pamplona](https://npub.world/npub1gcxzte5zlkncx26j68ez60fzkvtkm9e0vrwdcvsjakxf9mu9qewqlfnj5z).

The idea is to have `kind:30382` represent *assertions* made about an *entity*, which could be a *pubkey*, for example. The NIP also specifies a tag called `rank`, which can be used to build some basic WoT primitives.

Example event:

```json
{
  "kind": 30382,
  "tags": [
    ["d", "e88a6...50411"],
    ["rank", "89"],
    ["zap_amt_sent", "1000000"],
  ],
  "content": "",
  //...
}
```

However, we quickly realized that this NIP was too limiting for what we wanted to accomplish: *real-time, personalized ranking*.

To illustrate our different approaches, we will show how to replicate our [Verify Reputation](/docs/services/verify-reputation) with trusted assertions.

### Verify Reputation

[Verify Reputation](/docs/services/verify-reputation/) returns:
- the rank, follow count and followers count of the given `target`
- its top followers by rank.

All ranks are personalized to the given `source`.

This "batteries included" solution helps users avoid impersonation and fraud with a single request, no matter what application they are using, be it a kind:1 client, chat app, wallet, marketplace, or more.

![visual example of Verify Reputation](/images/verify_reputation_example.png)

To get the same result with trusted assertions, you would have to

- Get all contact lists that contain the `target` (as a `p` tag) 
- Build the target's `follower` list from those event authors
- Get the trusted assertions for the target and each of the followers
- Sort them by the rank provided by the trusted assertions

This operation is computationally expensive, especially on mobile devices. It is clear that if the user is willing to verify and process a large number of events (potentially +100k), he can compute some (rudimentary) ranking directly on the device, without the need to trust a third party.

**More generally, trusted assertions are very hard to use when one doesn't know the pubkeys of the people he's looking for.** **This goes fundamentally against our approach of Web of Trust, as a powerful mechanism for discovery.**

<div class="hx-mt-5"></div>

---

Are you looking to add Web of Trust capabilities to your app or project?  
[Take a look at our website](https://vertexlab.io/) or [send a DM to Pip](https://signal.me/#eu/O6mL1ozXhujHr-seJPkZzA4EKprDgr64AqoAOhu4U5TtKlRwutNJvbsCqXJvZb1-).