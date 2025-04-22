---
title: Sort Profiles
weight: 30
---

This service sorts a provided list of pubkeys using the specified algorithm.

Example use-cases:

 - Sorting comments under a note
 - Sorting zaps in a zap list
 - Ranking pubkeys' content

### Kinds

 - Request: 5314
 - Response: 6314
 - Error: 7000

### Request parameters

| Param | Type | Description | Default Value | Max |
|-----|-----|-----|-----|-----|
| `source` | string | The source pubkey used for personalized algorithms | The pubkey signing the DVM request | 1 |
| `target`  _(required)_  | string | Author pubkey to sort | - | 1000 |
| `sort` | string | Algorithm used to sort results | `globalPagerank` | 1 |
| `limit` | int | Maximum number of results returned in a response | `5` | `1000` |

Multiple `target` parameters SHOULD be supplied, as npubs or hex pubkeys.  
Sorting algorithms can be found [here](/docs/algos).

### Request

#### Example nak command
```
nak event -k 5314 --tag param="target;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" --tag param="target;f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2" --tag param="target;d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387" wss://relay.vertexlab.io
```

#### Example request event
```json
{
  "id": "1ded5cc51ffc9a77b11e20bc9ed1c84ae3bdfa557b30dafc97e9982641b17980",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1740755732,
  "kind": 5314,
  "tags": [
    [
      "param",
      "target",
      "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11",
    ],
    [
      "param",
      "target",
      "f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2",
    ],
    [
      "param",
      "target",
      "d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387",
    ],
  ],
  "content": "",
  "sig": "177f429bedc2ba7e32b631ae2abb2a68590b46abd18e3f2d13b4af5a3276c23d10a1f7123d0faa46d10378b4b7b5d192974457b2b2afd42ae67d47ce4c2d2050"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags. 
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | Requested target along with its rank |

Pubkeys are sorted in decreasing order by their ranks.

#### Example nak command
```
nak req -k 6314 -k 7000 --tag e=f15a41eee414a1242a174ed5f7389e7d83933147b3f0dfa70f5a642f074e525c wss://relay.vertexlab.io
```

#### Example response event

```json
{
  "id": "d7abd9710164c9982ee72c34f12bd49204384100e545d6a8ab96ad61097e175c",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1740755732,
  "kind": 6314,
  "tags": [
    [
      "e",
      "1ded5cc51ffc9a77b11e20bc9ed1c84ae3bdfa557b30dafc97e9982641b17980"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ],
    [
      "sort",
      "globalPagerank"
    ],
  ],
 "content":"[{\"pubkey\":\"726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11\",\"rank\":0.00016816930295038342},{\"pubkey\":\"f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2\",\"rank\":0.00008207271004088555},{\"pubkey\":\"d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387\",\"rank\":0}]",
  "sig": "8c8ac46872efc1c3f537243408c8d297e82086f2b5e533584f5352e6a2ca76a72151dfbff4b9e590b5968f519b063f268bec8f211a8f60a642acc7fe41516de7"
}
```

Formatted `content` JSON:

```json
[
	{
		"pubkey": "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11",
		"rank": 00016816930295038342
	},
	{
		"pubkey": "f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2",
		"rank": 0.00008207271004088555
	},
	{
		"pubkey": "d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387",
		"rank": 0
	},
]
```
