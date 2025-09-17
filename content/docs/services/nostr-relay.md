---
title: Nostr Relay
weight: 1
---

The Vertex Relay is the core endpoint that powers Vertex services. It provides free, reliable access to high-quality profile events aggregated from a wide range of popular relays.

Example use cases:
- Querying profile information without the risk of spam or low-quality data.
- Bootstrap social graphs with the most up-to-date events.
- Find users' preferred relays and blossom servers.

## Features

### Profile Discovery

The relay stores high-quality profile events.

- `kind:0` profile event  
- `kind:3` follow list  
- `kind:10000` mute list
- `kind:10002` relay list
- `kind:10063` blossom server list

This makes it easy for applications to find the latest profiles or follow lists, and bootstrap social graphs without having to scout the whole network.

### Read-Only Operation

To ensure the highest data quality and prevent spam, Vertex Relay operates in a read-only mode.  
Only our optimised [crawler](https://github.com/vertex-lab/crawler_v2) writes to the database,  ensuring a consistent, up-to-date, spam-free experience.
