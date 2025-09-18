---
title: Vertex is Now Free
date: 2025-09-17
summary: "Vertex is now easier than ever for developers to integrate. Starting today, all reputable pubkeys receive 100 free daily credits, removing the biggest barrier to building spam-proof apps without..."
---

Since our launch in February, Vertex has improved its services and earned the trust of projects that rely on us to make their apps spam-proof. Some of them include:

- [#asknostr](https://asknostr.site/)- a Q&A community on top of Nostr.
- [Satlantis](https://www.satlantis.io/) - the travel and community social network.
- [npub.world](https://npub.world/) - the nostr profile search engine.
- [Zapstore](https://zapstore.dev/) - the app store powered by your social graph.
- [Sovran](https://sovran.money/) - the Bitcoin and ecash wallet with built-in nostr search.

However, after speaking with many Nostr developers, we discovered an unexpected hurdle: the main barrier to adopting Vertex wasn’t cost, it was *authorization*.

## The Challenge

To fulfill a request with Vertex, you need to **sign it with a nostr key** that has sufficient credits, similar to how traditional APIs require an API key to authenticate each request and tie it to an account or quota.

But for most Nostr clients this is a major hurdle, because they are entirely client-side applications:

- They **don’t have, and don’t want, a backend** that could approve or sign requests on behalf of the app.
- They also **can’t safely embed a key** inside the app itself, as any secret can be extracted with enough effort.

As a result, even though many developers were happy to pay for Vertex’s features, the need for a backend stopped them from integrating it.

## What about ecash?

Ecash is a great technology and, in theory, the perfect solution to this problem.
Clients could prompt their users to embed ecash tokens inside each request, or tie them directly to the Vertex pubkey, completely removing the need for authorization.

But perfect technical solutions aren’t always practical.
Imagine being asked every time you want to search something with an annoying prompt like this one.

![Wanna Pay?](/images/wanna_pay.png)

The mental cost associated with these repeated decisions quickly becomes unbearable. Some of this can be automated, but not completely solved. And what happens if a user doesn’t even have an ecash wallet?

We’re still very bullish on ecash in the long term and plan to integrate it in the future, just not right now.

## Our Solution: Make It Free

Sometimes the simplest, “left-curve” solutions are the best ones. Starting today, all pubkeys* **get 100 free credits per day**.

Here is what this means:

- Clients can now sign requests with the **user’s own key**.  
- Users can access Vertex features **without paying anything**.  
- Developers no longer need to manage secrets or run backends to integrate Vertex.

![Make It Free Meme](/images/make_it_free_meme.jpg)

This change reflects our mission to make Vertex accessible, frictionless, and open for all developers. By removing the main barrier, we hope to empower Nostr developers to build apps with excellent UX — apps capable of onboarding millions of new users.

What are you waiting for? Check out our [docs](/docs/services) and **get started in 5 minutes!**

---

## FAQs

{{% details title="All pubkeys?" %}}

All reputable pubkeys receive 100 free credits per day. Currently, this includes over 200k pubkeys and is growing, essentially covering all active Nostr users. This simple restriction helps prevent spam.

{{% /details %}}

{{% details title="How much is 100 credits?" closed="true"%}}

It depends how you use them, but normally it's enough for multiple hours of searches, social activity, and spam filtering.

{{% /details %}}

{{% details title="How do you plan to monetize?" closed="true"%}}

We plan to earn revenue from clients who need more features or higher limits, and from relays or other apps running backends. For now, though, Nostr is still too small to focus on profits, it needs far more users. Adoption has slowed, likely due to a poor user experience. Offering this to the community is our way of helping the ecosystem grow.

{{% /details %}}

{{% details title="Do unused credits roll over?" closed="true"%}}

No, credits reset daily. This keeps the system fair and prevents stockpiling for spam.

{{% /details %}}

{{% details title="How can I get more credits?" closed="true"%}}

Yes. Higher limits and premium features are available for apps, relays, or heavy users.  
Check out our [pricing](/pricing) for more details.

{{% /details %}}

{{% details title="What happens if I run out of credits?" closed="true"%}}

Your requests will return an error event `kind:7000` until your daily credits refresh, or until you top up.

{{% /details %}}

{{% details title="Will Vertex be free forever?" closed="true"%}}

Forever is a strong word, but yes, that's the plan.

{{% /details %}}

{{% details title="Wen ecash?" closed="true"%}}

Soon™.

{{% /details %}}