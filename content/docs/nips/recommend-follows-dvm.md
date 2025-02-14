---
title: Recommend Follows DVM
weight: 20
---

([Nostr DVM draft PR](https://github.com/nostr-protocol/data-vending-machines/pull/38))

This service returns a list of recommended pubkeys (the ones with highest rank excluding source and its follows) sorted by an algorithm.

Example use-cases:
 - Offering users recommendations on accounts they may want to follow

### Kinds

 - Request: 5313
 - Response: 6313
 - Error: 7000

### Request parameters

| Parameter | Type | Description | Default Value |
|-----|-----|-----|-----|
| `source` | string | The source pubkey used for personalized algorithms | The pubkey signing the DVM request |
| `sort` | string | Algorithm used to sort results | `globalPagerank` |
| `limit` | int | Maximum number of results returned in a response | `5` |

Pubkeys can be in either hex or npub format.

#### Sorting algorithms

We currently support the following algorithms:

 - `globalPagerank`: Global Pagerank
 - `personalizedPagerank`: Personalized Pagerank

If you are unsure about which one to use, [read our FAQs](https://vertexlab.io/docs/faq/#what-is-the-difference-between-global-and-personalized-pagerank)

### Request

#### Example nak command
```
nak event -k 5313 --tag param="source;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" --tag param="sort;personalizedPagerank" wss://relay.vertexlab.io
```

#### Example request event

```json
{
  "id": "a9e2f8beae38626d5301e277b444db4533bd6f3fd9802f7abb9a6584911409c4",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1739563709,
  "kind": 5313,
  "tags": [
    [
      "param",
      "source",
      "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11"
    ],
    [
      "param",
      "sort",
      "personalizedPagerank"
    ],
  ],
  "content": "",
  "sig": "217e633bd86280efb543891560b207703f999bc47a76955438e99aea929d87cd88de3346cfe109da4ce6951d86b48dc6ef92987a2ba735fad79b5405a7836047"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags. 
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | Recommended hex nostr pubkey, along with its rank |

Pubkeys are sorted in decreasing order by their ranks.

#### Example nak command
```
nak req -k 6313 -k 7000 --tag e=a9e2f8beae38626d5301e277b444db4533bd6f3fd9802f7abb9a6584911409c4 wss://relay.vertexlab.io
```

#### Example response event

```json
{
  "id": "58020b72ca2ee10bcb7f103f3e3bd714b7e0bb0a4a521af6b9eece79f798686e",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1739563709,
  "kind": 6313,
  "tags": [
    [
      "e",
      "a9e2f8beae38626d5301e277b444db4533bd6f3fd9802f7abb9a6584911409c4"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ]
  ],
"content":"[{\"pubkey\":\"3efdaebb1d8923ebd99c9e7ace3b4194ab45512e2be79c1b7d68d9243e0d2681\",\"rank\":0.0036418093325618593},{\"pubkey\":\"f728d9e6e7048358e70930f5ca64b097770d989ccd86854fe618eda9c8a38106\",\"rank\":0.0017698512644225858},{\"pubkey\":\"c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0\",\"rank\":0.001701780061944794},{\"pubkey\":\"020f2d21ae09bf35fcdfb65decf1478b846f5f728ab30c5eaabcd6d081a81c3e\",\"rank\":0.001463530853272523},{\"pubkey\":\"a341f45ff9758f570a21b000c17d4e53a3a497c8397f26c0e6d61e5acffc7a98\",\"rank\":0.0013954596507947312}]",
  "sig": "8c061e8dcb4bc93f6639674a0516143ce2a8c30c453a3fdae3a6ddce6eaa238c9e5ef1bf358842b740f1d044ea8fa5205e7ce9d2a571f158885e19224d527bbc"
}
```

Formatted `content` JSON:

```json
[
	{
		"pubkey": "3efdaebb1d8923ebd99c9e7ace3b4194ab45512e2be79c1b7d68d9243e0d2681",
		"rank": 0.0036418093325618593
	},
	{
		"pubkey": "f728d9e6e7048358e70930f5ca64b097770d989ccd86854fe618eda9c8a38106",
		"rank": 0.0017698512644225858
	},
	{
		"pubkey": "c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0",
		"rank": 0.001701780061944794
	},
	{
		"pubkey": "020f2d21ae09bf35fcdfb65decf1478b846f5f728ab30c5eaabcd6d081a81c3e",
		"rank": 0.001463530853272523
	},
	{
		"pubkey": "a341f45ff9758f570a21b000c17d4e53a3a497c8397f26c0e6d61e5acffc7a98",
		"rank": 0.0013954596507947312
	}
]
```
