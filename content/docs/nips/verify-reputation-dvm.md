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
| `target` | string (required) | Pubkey the requester is interested in | - |
| `sort`| string | Algorithm used to sort results | `globalPagerank` |
| `limit` | int | Maximum number of results returned in a response | `5` |

Pubkeys can be in either hex or npub format.

#### Sorting algorithms

We currently support the following algorithms:

 - `globalPagerank`: Global Pagerank
 - `personalizedPagerank`: Personalized Pagerank

If you are unsure about which one to use, [read our FAQs](https://vertexlab.io/docs/faq/#what-is-the-difference-between-global-and-personalized-pagerank)

#### Example request

```json
{
  "id": "1cd2c73f53e602ae6f081997962bd43c730a565053080ab27ef7efb7335f7f49",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1732758297,
  "kind": 5312,
  "tags": [
    [
      "param",
      "source",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ],
    [
      "param",
      "target",
      "5097f9b8bd3ebb3c240a6a0c95bdf24d22a10211181f90ba29c41c31c889ba0a"
    ],
    [
      "param",
      "sort",
      "personalizedPagerank"
    ],
    [
      "param",
      "limit",
      7
    ],
  ],
  "content": "",
  "sig": "22f8aa10a0a3e9ef44f2b6a050868f46f19fcc1bbd9da3c3b291164405fb854a4b83524770d82d008a7415636554defcfb5ea52bf42e8a681a69ef10a81bc8e2"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags. 
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | a nostr hex Pubkey along with its rank |

The first pair is always the `pubkey` and `rank` of the `target` 
The subsequent pairs are the `pubkeys` and `ranks` of the top followers of `target` (decreasing order), determined by the algorithm specified in the `sort` parameter.

#### Example response

```json
{
  "id": "26594511e04ee1b20b94719616a2380b3dcaf0430e2fd6d4dcf59d24f9175fca",
  "pubkey": "a9b008476119ea693cbd2f0b4de99fd346e2e30880b18d42234a1158bd323783",
  "created_at": 1732758298,
  "kind": 6312,
  "tags": [
    [
      "e",
      "1cd2c73f53e602ae6f081997962bd43c730a565053080ab27ef7efb7335f7f49"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ]
  ],
  "content": "[{\"pubkey\":\"5097f9b8bd3ebb3c240a6a0c95bdf24d22a10211181f90ba29c41c31c889ba0a\",\"rank\":0.00066},
  {\"pubkey\":\"bd0c0615960ff21214aee7f5b06fa0a104585938c8eb4b5cd4e2b109041fdf62\",\"rank\":0.0025},{\"pubkey\":\"d05ab982e1105476ab68e4c6728d148f8e6222154e60cc359ef6b8599c820bea\",\"rank\":0.00163},{\"pubkey\":\"6efd1b46b3e6d1ec2447af7c905827bc83e1330bee2c3a6a5b8e0769734785e2\",\"rank\":0.00154},{\"pubkey\":\"bb17f1e4e516e75e82a5b5e81c0120ffeb24e9e92866962440b9888ae82e42a1\",\"rank\":0.00111}]",
  "sig": "3c25ff7f8d6d847775a9aafb8b1f28d2f2e9b53f78de7f53b49fbbe46402358dc281be263c20919a426cbea86fbe9d36951fd5dd86465181d9d49be056616f53"
}
```

Formatted `content` JSON:

```json
[
  	{
		"pubkey": "5097f9b8bd3ebb3c240a6a0c95bdf24d22a10211181f90ba29c41c31c889ba0a",
		"rank": 0.00066
	},
	{
		"pubkey": "bd0c0615960ff21214aee7f5b06fa0a104585938c8eb4b5cd4e2b109041fdf62",
		"rank": 0.00250
	},
	{
		"pubkey": "d05ab982e1105476ab68e4c6728d148f8e6222154e60cc359ef6b8599c820bea",
		"rank": 0.00163
	},
	{
		"pubkey": "6efd1b46b3e6d1ec2447af7c905827bc83e1330bee2c3a6a5b8e0769734785e2",
		"rank": 0.00154
	},
	{
		"pubkey": "bb17f1e4e516e75e82a5b5e81c0120ffeb24e9e92866962440b9888ae82e42a1",
		"rank": 0.00111
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
