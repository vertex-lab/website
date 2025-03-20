---
title: Verify Reputation DVM
weight: 10
---

([Nostr DVM draft PR](https://github.com/nostr-protocol/data-vending-machines/pull/38))

This service returns reputation information about a pubkey, including a list of pubkeys who follow it sorted by an algorithm.

Example use-cases:

  - Helping users assess the reputation of a npub
  - Showing relevant followers of that npub
  - Reducing the probability of successful impersonators

### Kinds

 - Request: 5312
 - Response: 6312
 - Error: 7000

### Request parameters

| Parameter | Type | Description | Default Value |
|-----|-----|-----|-----|
| `source` | string | The source pubkeys used for personalized algorithms | The pubkey signing the DVM request |
| `target` | string _(required)_ | Pubkey the requester is interested in | - |
| `sort`| string | Algorithm used to sort results | `globalPagerank` |
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
nak event -k 5312 --tag param="source;04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9" --tag param="sort;personalizedPagerank" --tag param="target;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" wss://relay.vertexlab.io
```

#### Example request event

```json
{
  "id": "07f071aa6945df0ed9c830314b99f5ea41afed3563efa193f712487d9e846d58",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1742485108,
  "kind": 5312,
  "tags": [
    [
      "param",
      "source",
      "04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9"
    ],
    [
      "param",
      "sort",
      "personalizedPagerank"
    ],
    [
      "param",
      "target",
      "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11"
    ],
  ],
  "content": "",
  "sig": "0cd00a0bca17eb89413f84d4e12d69ae8de2bb9fd1c61f7b6c33e4367135c32cebcd4dfa33cea25f28aa9c68854a50650013bcc4e83f8074ada3da4654b83967"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags.
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | a nostr hex Pubkey along with its rank |

The first object is always the `pubkey` and `rank` of the `target`, as well as its `follows` and `followers` counts (`int`). The follower count does not take into account spammers or bots, as they do not pass our internal filters.

The subsequent pairs are the `pubkeys` and `ranks` of the top followers of `target` (descending order), determined by the algorithm specified in the `sort` parameter.

#### Example nak command
```
nak req -k 6312 -k 7000 --tag e=07f071aa6945df0ed9c830314b99f5ea41afed3563efa193f712487d9e846d58 wss://relay.vertexlab.io
```

#### Example response event

```json
{
  "id": "2ad269a0160b64682be86fd5f3b9d0b48587b450f7c1c6375573eddec60eb2d5",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1742485108,
  "kind": 6312,
  "tags": [
    [
      "e",
      "07f071aa6945df0ed9c830314b99f5ea41afed3563efa193f712487d9e846d58"
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
  "content":"[{\"pubkey\":\"726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11\",\"rank\":0.00034409542913234554,\"follows\":535, \"followers\":1978},{\"pubkey\":\"04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9\",\"rank\":0.2365482693862695},{\"pubkey\":\"32e1827635450ebb3c5a7d12c1f8e7b2b514439ac10a67eef3d9fd9c5c68e245\",\"rank\":0.002478020571038372},{\"pubkey\":\"6e468422dfb74a5738702a8823b9b28168abab8655faacb6853cd0ee15deee93\",\"rank\":0.0023286458111049426},{\"pubkey\":\"472f440f29ef996e92a186b8d320ff180c855903882e59d50de1b8bd5669301e\",\"rank\":0.002203277709017957},{\"pubkey\":\"3bf0c63fcb93463407af97a5e5ee64fa883d107ef9e558472c4eb9aaaefa459d\",\"rank\":0.002197942896163192}]",
  "sig": "d96ac2b173fcf693382b104d8276b94ee5a3ee32788fbc37d06367bfecf409f84dd1219c449d696bacce314be4a8bc01f3cd6c062cce3c98613a92f2aaa63c01"
}
```

Formatted `content` JSON:

```json
[
  	{
		"pubkey": "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11",
		"rank": 0.00034409542913234554,
        "follows": 535,
        "followers": 1978
	},
	{
		"pubkey": "04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9",
		"rank": 0.2365482693862695
	},
	{
		"pubkey": "32e1827635450ebb3c5a7d12c1f8e7b2b514439ac10a67eef3d9fd9c5c68e245",
		"rank": 0.002478020571038372
	},
	{
		"pubkey": "6e468422dfb74a5738702a8823b9b28168abab8655faacb6853cd0ee15deee93",
		"rank": 0.0023286458111049426
	},
	{
		"pubkey": "472f440f29ef996e92a186b8d320ff180c855903882e59d50de1b8bd5669301e",
		"rank": 0.002203277709017957
	},
	{
		"pubkey": "3bf0c63fcb93463407af97a5e5ee64fa883d107ef9e558472c4eb9aaaefa459d",
		"rank": 0.002197942896163192
	},
]
```

#### Example error response

```json
{
  "kind": 7000,
  "id": "96f6eec0b3e410db91b85bee714740ec76ec64a9eb60a5503fee4dfc02047c1a",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1738635799,
  "tags": [
      [
        "status",
        "error",
        "badly formatted key: npub1"
      ],
      [
        "e",
        "1cd2c73f53e602ae6f081997962bd43c730a565053080ab27ef7efb7335f7f49"
      ],
      [
        "p",
        "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
      ]
  ],
  "content": "",
  "sig": "6318c7ce6c57dd85779faadc4fed7733a1e50bd46205d52c96e535648dd2dd07e573e8d1fdef496d27014c80d1f2f604cb5337744d24000c00dde485ccdcf48c"
}
```
