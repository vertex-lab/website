---
title: Sort Profiles DVM
weight: 30
---

([Nostr DVM draft PR](https://github.com/nostr-protocol/data-vending-machines/pull/38))

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

| Parameter | Type | Description | Default Value |
|-----|-----|-----|-----|
| `source` | string | The source pubkeys used for personalized algorithms | The pubkey signing the DVM request |
| `target` | string _(required)_  | Author pubkey to sort | - |
| `sort` | string | Algorithm used to sort results | `globalPagerank` |
| `limit` | int | Maximum number of results returned in a response | `5` |

Pubkeys can be in either hex or npub format.
Only one `source` can be supplied.
Multiple `target` parameters SHOULD be supplied.

#### Sorting algorithms

We currently support the following algorithms:

 - `globalPagerank`: Global Pagerank
 - `personalizedPagerank`: Personalized Pagerank

If you are unsure about which one to use, [read our FAQs](https://vertexlab.io/docs/faq/#what-is-the-difference-between-global-and-personalized-pagerank)

### Request

#### Example nak command
```
nak event -k 5314 --tag param="target;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" --tag param="target;f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2" --tag param="target;d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387" wss://relay.vertexlab.io
```

#### Example request event
```json
{
  "id": "f15a41eee414a1242a174ed5f7389e7d83933147b3f0dfa70f5a642f074e525c",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1739562990,
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
  "sig": "996e243478504aaec423ddbd17d77ee343dae85b65062edeea1d58306185aa9994d221b08512953a55ea3f1783c4c86d108929371296897e44bebd7d53718f9a"
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
  "id": "421d49864f791ba7a39d0ede71ad419571d6bae194d8fddf04e9e4a7b8b4bac3",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1739562990,
  "kind": 6314,
  "tags": [
    [
      "e",
      "f15a41eee414a1242a174ed5f7389e7d83933147b3f0dfa70f5a642f074e525c"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ]
  ],
  "content": "[{\"pubkey\":\"726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11\",\"rank\":0.0001607724330728076},{\"pubkey\":\"f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2\",\"rank\":0.00007583312883188345},{\"pubkey\":\"d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387\",\"rank\":0}]",
  "sig": "3bbf45f522e638fe5a2723d97f8db79ae4438407c2b2e8e98bddceb959bc700c9e343fde22e5fc873295c8b9bdb163512daa75ef77b78fea875c2a4ceef4a557"
}
```

Formatted `content` JSON:

```json
[
	{
		"pubkey": "726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11",
		"rank": 0.0001607724330728076
	},
	{
		"pubkey": "f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2",
		"rank": 0.00007583312883188345
	},
	{
		"pubkey": "d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387",
		"rank": 0
	},
]
```
