---
title: Recommend Follows
weight: 20
---


This service returns a list of recommended pubkeys (the ones with highest rank excluding source and its follows) sorted by an algorithm.

Example use-cases:
 - Offering users recommendations on accounts they may want to follow

### Kinds

 - Request: 5313
 - Response: 6313
 - Error: 7000

### Request

#### Parameters

| Param | Type | Description | Default Value | Max |
|-----|-----|-----|-----|-----|
| `sort` | string | Algorithm used to sort results | `globalPagerank` | 1 |
| `source` | string | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | 1 |
| `limit` | int | Maximum number of results returned | `5` | `100` |

Pubkeys can be in either hex or npub format.  
Sorting algorithms can be found [here](/docs/algos).

#### Example nak command
```
nak event -k 5313 --tag param="sort;personalizedPagerank" --tag param="source;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" wss://relay.vertexlab.io
```

#### Example request

```json
{
  "id": "a130189ceed11faf9f634f781e850e2269882fa86e560b5a3ead7d202747dfc1",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1745935066,
  "kind": 5313,
  "tags": [
    [
      "param",
      "sort",
      "personalizedPagerank"
    ],
    [
      "param",
      "source",
      "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11"
    ],
  ],
  "content": "",
  "sig": "7c1d0e0fca1e8414973e7f20869447190fbd376466b46052b15be7e6021bfd7f64940225094374fc2c6834ce9562d5bd1fde07c08d3522e81fe8be46bb7457fd"
}
```

### Response

#### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `sort`  | The sorting algorithm specified in the request                              |
| `source`| The source specified in the request (or the pubkey if not specified) |
| `nodes` | The number of nodes in the graph at the time the request was made           |

#### Content

The `content` field is a JSON-stringified array of objects, each formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| `pubkey` | string | a nostr hex pubkey|
| `rank` | float | the rank computed with the `sort` algorithm |

The recommended pubkeys don't include the `source` or its follows, and are sorted in descending order by their `rank`.

#### Example nak command
```
nak req -k 6313 -k 7000 --tag e=a9e2f8beae38626d5301e277b444db4533bd6f3fd9802f7abb9a6584911409c4 wss://relay.vertexlab.io
```

#### Example response

```json
{
  "id": "35fb47afd9ff1c947f6505f98fcafb0d246f026f4c19985d0bd58dce32a63fdb",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1745935066,
  "kind": 6313,
  "tags": [
    [
      "e",
      "a130189ceed11faf9f634f781e850e2269882fa86e560b5a3ead7d202747dfc1"
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
      "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11"
    ],
    [
      "nodes",
      "317328",
    ],
  ],
  "content":"[{\"pubkey\":\"3efdaebb1d8923ebd99c9e7ace3b4194ab45512e2be79c1b7d68d9243e0d2681\",\"rank\":0.0037502524208359394},{\"pubkey\":\"c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0\",\"rank\":0.0016058773186656803},{\"pubkey\":\"f728d9e6e7048358e70930f5ca64b097770d989ccd86854fe618eda9c8a38106\",\"rank\":0.0014520208090929205},{\"pubkey\":\"a341f45ff9758f570a21b000c17d4e53a3a497c8397f26c0e6d61e5acffc7a98\",\"rank\":0.0014472127931687718},{\"pubkey\":\"020f2d21ae09bf35fcdfb65decf1478b846f5f728ab30c5eaabcd6d081a81c3e\",\"rank\":0.0013750925543065406}]", 
  "sig":"fcb8db6d1d339dd896bc1320411c2091ffb0327fa00798a5dbe2bfd5e8866f73ff0ecbef4ab06bc60e35c8496e1e7cf8816bde489e88daf5163aef56d9b75382"
}
```

Formatted `content` JSON:

```json
[
	{
		"pubkey": "3efdaebb1d8923ebd99c9e7ace3b4194ab45512e2be79c1b7d68d9243e0d2681",
		"rank": 0.0037502524208359394
	},
	{
		"pubkey": "c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0",
		"rank": 0.0016058773186656803
	},
	{
		"pubkey": "f728d9e6e7048358e70930f5ca64b097770d989ccd86854fe618eda9c8a38106",
		"rank": 0.0014520208090929205
	},
	{
		"pubkey": "a341f45ff9758f570a21b000c17d4e53a3a497c8397f26c0e6d61e5acffc7a98",
		"rank": 0.0014472127931687718
	},
	{
		"pubkey": "020f2d21ae09bf35fcdfb65decf1478b846f5f728ab30c5eaabcd6d081a81c3e",
		"rank": 0.0013750925543065406
	}
]
```

### Error

#### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `status`| The error type and error message                              |

#### Example error

```json
{
  "kind": 7000,
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
        "invalid source: this is not a valid public key"
      ],
  ],
  // ...
}
```
