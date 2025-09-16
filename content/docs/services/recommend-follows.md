---
title: Recommend Follows
weight: 20
---


This service returns a list of recommended pubkeys (the ones with highest rank excluding source and its follows) sorted by an algorithm.

Example use-cases:
 - Offering users recommendations on accounts they may want to follow

## Kinds

 - Request: `5313`
 - Response: `6313`
 - Error: `7000`

## Request

### Parameters

All parameters must be passed as *strings* in the tags of the Request event, with the following format:

```json
["param", "<name>", "<value>"] 
```

| Param | Description | Default Value | Max |
|-----|-----|-----|-----|
| `sort` | Algorithm used to sort results | `"globalPagerank"` | one sort |
| `source` | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | one source |
| `limit` | Maximum number of results returned | `"5"` | `"100"` |

Pubkeys can be in either hex or npub format.  
Sorting algorithms can be found [here](/docs/algos).

### Example nak command
```
nak event -k 5313 --tag param="sort;personalizedPagerank" --tag param="source;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" wss://relay.vertexlab.io
```

### Example request

```json
{
  "id": "a13018...47dfc1",
  "pubkey": "79be66...f81798",
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
      "726a1e...1a1c11"
    ],
  ],
  "content": "",
  "sig": "7c1d0e...7457fd"
}
```

## Response

### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `sort`  | The sorting algorithm specified in the request                              |
| `source`| The source specified in the request (or the pubkey if not specified) |
| `nodes` | The number of nodes in the graph at the time the request was made           |

### Content

The `content` field is a JSON-stringified array of objects, each formatted as:

| Field | Type | Description |
|-----|-----|-----|
| `pubkey` | string | a nostr hex pubkey|
| `rank` | float | the rank computed with the `sort` algorithm |

The recommended pubkeys don't include the `source` or its follows, and are sorted in descending order by their `rank`.

### Example nak command
```
nak req -k 6313 -k 7000 --tag e=a130189ceed11faf9f634f781e850e2269882fa86e560b5a3ead7d202747dfc1 wss://relay.vertexlab.io
```

### Example response

```json
{
  "id": "35fb47...a63fdb",
  "pubkey": "5fc48a...ccaeb4",
  "created_at": 1745935066,
  "kind": 6313,
  "tags": [
    [
      "e",
      "a13018...47dfc1"
    ],
    [
      "p",
      "79be66...f81798"
    ],
    [
      "sort",
      "personalizedPagerank"
    ],
    [
      "source",
      "726a1e...1a1c11"
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
		"pubkey": "3efdae...0d2681",
		"rank": 0.0037502524208359394
	},
	{
		"pubkey": "c4eaba...cbc4e0",
		"rank": 0.0016058773186656803
	},
	{
		"pubkey": "f728d9...a38106",
		"rank": 0.0014520208090929205
	},
	{
		"pubkey": "a341f4...fc7a98",
		"rank": 0.0014472127931687718
	},
	{
		"pubkey": "020f2d...a81c3e",
		"rank": 0.0013750925543065406
	}
]
```

## Error

### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `status`| The error type and error message                              |

### Example error

```json
{
  "kind": 7000,
  "tags": [
      [
        "e",
        "1cd2c7...5f7f49"
      ],
      [
        "p",
        "79be66...f81798"
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
