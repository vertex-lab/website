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

#### Example request

This example uses no parameters, gets recommended follows for the signer.

```json
{
  "id": "588d828025eab6404ed17c6c7a70d09a67c5da4ffe780e2f943f32509fe8af23",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1732759754,
  "kind": 5313,
  "tags": [],
  "content": "",
  "sig": "e0174ad1416e0d722f06491909fe8e4781fd732c21df6424cf0f1dc422db53ba525d544a29927297f63543796750ed7abf5e3c10c0e40e72b8c916b9a751c078"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags. 
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | Recommended hex nostr pubkey, along with its rank |

Pubkeys are sorted in decreasing order by their ranks.

#### Example response

```json
{
  "id": "171a0a7551c785ab0e2ac99577bfc25dd4fe7c28a19cacfcb625b4be2964ea4a",
  "pubkey": "a9b008476119ea693cbd2f0b4de99fd346e2e30880b18d42234a1158bd323783",
  "created_at": 1732759756,
  "kind": 6313,
  "tags": [
    [
      "e",
      "588d828025eab6404ed17c6c7a70d09a67c5da4ffe780e2f943f32509fe8af23"
    ],
    [
      "p",
      "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798"
    ]
  ],
"content": "[{\"pubkey\":\"bd0c0615960ff21214aee7f5b06fa0a104585938c8eb4b5cd4e2b109041fdf62\",\"rank\":0.0025},{\"pubkey\":\"d05ab982e1105476ab68e4c6728d148f8e6222154e60cc359ef6b8599c820bea\",\"rank\":0.00163},{\"pubkey\":\"6efd1b46b3e6d1ec2447af7c905827bc83e1330bee2c3a6a5b8e0769734785e2\",\"rank\":0.00154},{\"pubkey\":\"bb17f1e4e516e75e82a5b5e81c0120ffeb24e9e92866962440b9888ae82e42a1\",\"rank\":0.00111},{\"pubkey\":\"5097f9b8bd3ebb3c240a6a0c95bdf24d22a10211181f90ba29c41c31c889ba0a\",\"rank\":0.000107}]",
  "sig": "c79f34b9f5603b242e00f0b04782d579ffcec2cb45e511fbbf1ba3e04d5297f7eb7a071433b0a14300fbd766feaf5e8e1f6fbd216ae1cce1cb400f987fc2d0d2"
}
```

Formatted `content` JSON:

```json
[
	{
		"pubkey": "bd0c0615960ff21214aee7f5b06fa0a104585938c8eb4b5cd4e2b109041fdf62",
		"rank": 0.0025
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
	{
		"pubkey": "5097f9b8bd3ebb3c240a6a0c95bdf24d22a10211181f90ba29c41c31c889ba0a",
		"rank": 0.000107
	}
]
```
