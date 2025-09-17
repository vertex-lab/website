---
title: "Beyond IPs—Reputation based rate limiting"
date: 2025-04-28
summary: "Nostr is a simple and powerful protocol for decentralized communication. Thanks to its minimalist design — just public keys, signed messages, and open relays — it's easy to use, easy to build on, and hard to censor. But with that openness comes a big challenge: it's also easy for bad actors to spam the network..."
math: true
---

Nostr is a simple and powerful protocol for decentralized communication. Its minimalist design — just public keys, signed messages, and open relays — makes it easy to use, easy to build on, and hard to censor. But that same openness also makes it easy for bad actors to spam the network and flood relays.

Since anyone can spin up new key pairs instantly, traditional ways of preventing abuse, like blacklisting, simply don't work. Micropayments are part of the solution, but they can't solve everything, for example if the business model requires indirect or delayed monetization (e.g. free trials).

To keep Nostr open and permissionless while protecting relays, we need smarter traffic management. One promising approach is reputation-based rate limiting: adjusting a pubkey's read/write access based on its behavior. Good actors get more freedom; new, suspicious, or spammy users face tighter limits.

In this article, we'll explore a simple and novel design that uses Data Vending Machines (DVMs) to seamlessly integrate reputation-based limits into a relay.

## The Budget

Every Nostr relay, especially open ones, faces an **optimization problem**:  
it wants to accept as many good events as possible (the ones users actually want) while filtering out spam — all without overloading or slowing down.

To meet the third requirement, a relay has an implicit or explicit **budget**, that is, how many requests it is willing or able to handle in a unit of time. Let's call this quantity $B$.

Today, many popular relays (such as Damus) split this budget uniformly: each pubkey gets the same number of events per time period. We call this quantity the pubkey's budget $p_B$, which in this case is

$$
p_B = B \cdot \frac{1}{N}
$$

Where $N$ is the number of users.  
For open relays, $N$ is unbounded, making uniform allocation wasteful. New or spammy pubkeys get as much bandwidth as trusted, high-quality ones.

We can do better by tying $p_B$ to the pubkey's reputation. New or suspicious pubkeys get tighter limits, while reputable ones—whose events are more valuable—get more freedom.

In the proposed design, we define $p_B$ as a function of the pubkey's global Pagerank $r_p$:

$$
p_B = B \cdot r_p
$$

## The How

One of the simplest and most effective rate-limiting techniques is the **Token Bucket** algorithm.
Each entity (in our case, a pubkey) is given a bucket that holds a certain number of tokens. Each event sent consumes some tokens, and tokens are replenished over time.

![Token Bucket Algorithm Visualized](/images/token_bucket.png)

The beauty of this approach is its simplicity: it's easy to implement and customize.  
In our design, the **refill rate depends on the pubkey's rank**.

Now, how do we compute these ranks?  
That's where specialized Data Vending Machines (DVMs) come in. Instead of burdening relays with heavy computation, we outsource it to efficient service providers. For example, batched DVMs like [RankProfiles](/docs/services/rank-profiles) can return ranks for up to 1,000 pubkeys in a single request for less than a penny. The relay simply queues up all the unknown pubkeys that try to write to it, and when the queue fills, it fetches their ranks.

There's another problem: An attacker could generate endless new pubkeys, flooding the queue and wasting backend resources.

To prevent this, we add a second layer of rate limiting:
We limit how many unknown pubkeys a single IP address can add to the queue over time.
While IPs are relatively cheap to rotate, they're still expensive enough to tip the balance in favor of defenders.

![](/images/reputation_rate_limiting_flow.png)

Let's crunch some numbers. Let's limit the number of unknown pubkeys an IP can send to the ranking queue in a day to 100.
The attacker needs 10 IPs to fill one DVM request, which costs (less than) 0.009 usd at current rates.  
If we assume that the cost of renting an IP for the shortest duration is 1¢, then the cost of launching an attack using 10 IPs would be 10 × 1¢ = 10¢.

This results in a cost asymmetry between attacking and defending that's 100 times greater, strongly favoring the defender.

## The Code

At [this link](https://github.com/pippellia-btc/rely/tree/main/examples/wot) you can find a minimal implementation with limited dependencies of the rate-limiting system. Feel free to review, modify, and integrate the code into your own project. It is designed to be flexible and can be adapted to a variety of use cases where rate limiting is required to prevent abuse.

## Quick Recap

Our design enforces two budgets:
- **IP budget**: limits how many unknown pubkeys each IP can send to the ranking queue.
- **Pubkey budget**: based on the pubkey's global Pagerank, limits how many events it can write to the relay.
