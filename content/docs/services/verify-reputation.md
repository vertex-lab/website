---
title: Verify Reputation
weight: 10
---

This service returns reputation information about a pubkey, including a list of pubkeys who follow it sorted by an algorithm. [Try it live on npub.world](https://npub.world/npub1kpt95rv4q3mcz8e4lamwtxq7men6jprf49l7asfac9lnv2gda0lqdknhmz).

Example use-cases:

  - Helping users assess the reputation of a npub
  - Showing relevant followers of that npub
  - Reducing the probability of successful impersonators

{{< callout type="info" >}}
Need the ranks of many pubkeys?  
Try [Rank Profiles](/docs/services/rank-profiles) for a fast, batched solution.
{{< /callout >}}

### Kinds

 - Request: 5312
 - Response: 6312
 - Error: 7000

### Request parameters

| Param | Type | Description | Default Value | Max |
|-----|-----|-----|-----| ----- |
| `target` _(required)_  | string| Pubkey whose reputation is being requested | - | 1 |
| `sort`| string | Algorithm used to sort results | `globalPagerank` | 1 |
| `source` | string | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | 1 |
| `limit` | int | Maximum number of results returned | `5` | `100` |

Pubkeys can be in either hex or npub format.  
Sorting algorithms can be found [here](/docs/algos).

### Request

#### Example nak command
```
nak event -k 5312 --tag param="target;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" wss://relay.vertexlab.io
```

#### Example request event

```json
{
  "id": "56729c03239d0d80d0641caa4561c55a061cd2f30f1fe017efe712b37e8fcb0c",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1745933480,
  "kind": 5312,
  "tags": [
    [
      "param",
      "target",
      "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11"
    ],
  ],
  "content": "",
  "sig": "ba113d347aa064603193751fd74862e2e8490ba1f1dbed3a1e660212d80a4adcbed0983e3c5f0363d814311c9713db701e72379f1c3c2579a045fc817d8a4112"
}
```

### Response

#### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `sort`  | The sorting algorithm specified in the request                              |
| `source`| The source specified in the request (present only if `sort=personalizedPagerank`) |
| `nodes` | The number of nodes in the graph at the time the request was made           |

#### Content

The `content` field is a JSON-stringified array of objects, each formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| `pubkey` | string | a nostr hex pubkey|
| `rank` | float | the rank computed with the `sort` algorithm|


The first object always contains the `pubkey` and `rank` of the `target`, as well as its `follows` and `followers` counts (`int`).

The subsequent pairs are the `pubkey`s and `rank`s of the top followers of `target`, sorted in descending order by their `rank`.

#### Example nak command
```
nak req -k 6312 -k 7000 --tag e=56729c03239d0d80d0641caa4561c55a061cd2f30f1fe017efe712b37e8fcb0c wss://relay.vertexlab.io
```

#### Example response event

```json
{
  "id": "c9670c7cb3ed65bdd10319ebf31f1905007965226b97e3b5cd2d61b528b575af",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1745933480,
  "kind": 6312,
  "tags": [
    [
      "e",
      "56729c03239d0d80d0641caa4561c55a061cd2f30f1fe017efe712b37e8fcb0c"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ],
    [
      "sort",
      "globalPagerank"
    ],
    [
      "nodes",
      "317328"
    ]
  ],
  "content":"[{\"pubkey\":\"726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11\",\"rank\":0.00018008434745346786,\"follows\":550,\"followers\":2102},{\"pubkey\":\"32e1827635450ebb3c5a7d12c1f8e7b2b514439ac10a67eef3d9fd9c5c68e245\",\"rank\":0.0038190735362639222},{\"pubkey\":\"04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9\",\"rank\":0.0023781006037430445},{\"pubkey\":\"3bf0c63fcb93463407af97a5e5ee64fa883d107ef9e558472c4eb9aaaefa459d\",\"rank\":0.0020976712437000707},{\"pubkey\":\"472f440f29ef996e92a186b8d320ff180c855903882e59d50de1b8bd5669301e\",\"rank\":0.0014937791227049887},{\"pubkey\":\"3f770d65d3a764a9c5cb503ae123e62ec7598ad035d836e2a810f3877a745b24\",\"rank\":0.0014543235565134729}]",
  "sig":"a16fbad8d49768fe8c764b12221bf823f2095d333ac25bc7d8766b6dc42c26f3ce5e365843ca9fbb36eb38fc2b277b4e33ce683610c199f23f7b90c462577f91"
}
```

Formatted `content` JSON:

```json
[
  	{
		"pubkey": "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11",
		"rank": 0.00018008434745346786,
        "follows": 550,
        "followers": 2102
	},
	{
		"pubkey": "32e1827635450ebb3c5a7d12c1f8e7b2b514439ac10a67eef3d9fd9c5c68e245",
		"rank": 0.0038190735362639222
	},
	{
		"pubkey": "04c915daefee38317fa734444acee390a8269fe5810b2241e5e6dd343dfbecc9",
		"rank": 0.0023781006037430445
	},
	{
		"pubkey": "3bf0c63fcb93463407af97a5e5ee64fa883d107ef9e558472c4eb9aaaefa459d",
		"rank": 0.0020976712437000707
	},
	{
		"pubkey": "472f440f29ef996e92a186b8d320ff180c855903882e59d50de1b8bd5669301e",
		"rank": 0.0014937791227049887
	},
	{
		"pubkey": "3f770d65d3a764a9c5cb503ae123e62ec7598ad035d836e2a810f3877a745b24",
		"rank": 0.0014543235565134729
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
        "e",
        "1cd2c73f53e602ae6f081997962bd43c730a565053080ab27ef7efb7335f7f49"
      ],
      [
        "p",
        "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
      ],
      [
        "status",
        "error",
        "badly formatted key: npub1"
      ],
  ],
  "content": "",
  "sig": "6318c7ce6c57dd85779faadc4fed7733a1e50bd46205d52c96e535648dd2dd07e573e8d1fdef496d27014c80d1f2f604cb5337744d24000c00dde485ccdcf48c"
}
```
