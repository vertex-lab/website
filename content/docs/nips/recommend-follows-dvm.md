---
title: Recommend Follows DVM
weight: 20
---


This service returns a list of recommended pubkeys (the ones with highest rank excluding source and its follows) sorted by an algorithm.

Example use-cases:
 - Offering users recommendations on accounts they may want to follow

### Kinds

 - Request: 5313
 - Response: 6313
 - Error: 7000

### Request parameters

| Param | Type | Description | Default Value | Max |
|-----|-----|-----|-----|-----|
| `source` | string | The source pubkey used for personalized algorithms | The pubkey signing the DVM request | 1 |
| `sort` | string | Algorithm used to sort results | `globalPagerank` | 1 |
| `limit` | int | Maximum number of results returned in a response | `5` | 1000 |

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
  "id": "cc2c3b40e18e6fc89d7d610c0ab4206433e2113cd3d2761bd65f42dfae13db2f",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1740755573,
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
  "sig": "4e2cbf41da885e0a4e380ffac7dffd4bffdc802ad1db3ac00c33c8ca36a2cba9e3829064cd0569c58788d6b7e0a0fc30dcdde7b0d281e5f9a0b9e87ea2e610bd"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags. 
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | Recommended hex nostr pubkey, along with its rank |

Pubkeys are sorted in descending order by their ranks.

#### Example nak command
```
nak req -k 6313 -k 7000 --tag e=a9e2f8beae38626d5301e277b444db4533bd6f3fd9802f7abb9a6584911409c4 wss://relay.vertexlab.io
```

#### Example response event

```json
{
  "id": "0accd69642c5169b2af04b5b94e946b630e54dac44989a3cb668f7fb7ca3eb32",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1740755573,
  "kind": 6313,
  "tags": [
    [
      "e",
      "cc2c3b40e18e6fc89d7d610c0ab4206433e2113cd3d2761bd65f42dfae13db2f"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ],
    [
      "sort",
      "personalizedPagerank"
    ],
    [
      "source",
      "04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9"
    ]
  ],
"content":"[{\"pubkey\":\"3efdaebb1d8923ebd99c9e7ace3b4194ab45512e2be79c1b7d68d9243e0d2681\",\"rank\":0.0038803226794649343},{\"pubkey\":\"c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0\",\"rank\":0.0017699717485278592},{\"pubkey\":\"f728d9e6e7048358e70930f5ca64b097770d989ccd86854fe618eda9c8a38106\",\"rank\":0.0015997821573232574},{\"pubkey\":\"a341f45ff9758f570a21b000c17d4e53a3a497c8397f26c0e6d61e5acffc7a98\",\"rank\":0.0012594029749140537},{\"pubkey\":\"090254801a7e8e5085b02e711622f0dfa1a85503493af246aa42af08f5e4d2df\",\"rank\":0.001191327138432213}]",
  "sig": "81db2c7e2ee73062de1a81a9216948169132b52355a1924bfcc366e49afd34b01ce3e02a3ffc7e8e586918f4a7a610f7ec6b7df7885a2f7d58e83d0ac59c07c1"
}
```

Formatted `content` JSON:

```json
[
	{
		"pubkey": "3efdaebb1d8923ebd99c9e7ace3b4194ab45512e2be79c1b7d68d9243e0d2681",
		"rank": 0.0038803226794649343
	},
	{
		"pubkey": "c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0",
		"rank": 0.0017699717485278592
	},
	{
		"pubkey": "f728d9e6e7048358e70930f5ca64b097770d989ccd86854fe618eda9c8a38106",
		"rank": 0.0015997821573232574
	},
	{
		"pubkey": "a341f45ff9758f570a21b000c17d4e53a3a497c8397f26c0e6d61e5acffc7a98",
		"rank": 0.0012594029749140537
	},
	{
		"pubkey": "090254801a7e8e5085b02e711622f0dfa1a85503493af246aa42af08f5e4d2df",
		"rank": 0.001191327138432213
	}
]
```
