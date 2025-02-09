---
title: Sort Authors DVM
weight: 30
---

([Nostr DVM draft PR](https://github.com/nostr-protocol/data-vending-machines/pull/38))

This service sorts a provided list of pubkeys using the specified algorithm.

Example use-cases:

 - Sorting comments under a note
 - Sorting zaps in a zap list
 - Surfacing relevant pubkeys' content in search results

### Kinds

 - Request: 5314
 - Response: 6314
 - Error: 7000

### Request parameters

| Parameter | Type | Description | Default Value |
|-----|-----|-----|-----|
| `source` | string | The source pubkeys used for personalized algorithms | The pubkey signing the DVM request |
| `target` _(required)_ | string | Author pubkey to sort | - |
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

#### Example request

```json
{
  "id": "588d828025eab6404ed17c6c7a70d09a67c5da4ffe780e2f943f32509fe8af23",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
  "created_at": 1732760317,
  "kind": 5314,
  "tags": [
    [
      "param",
      "target",
      "d05ab982e1105476ab68e4c6728d148f8e6222154e60cc359ef6b8599c820bea",
    ],
    [
      "param",
      "target",
      "6efd1b46b3e6d1ec2447af7c905827bc83e1330bee2c3a6a5b8e0769734785e2",
    ],
    [
      "param",
      "target",
      "bd0c0615960ff21214aee7f5b06fa0a104585938c8eb4b5cd4e2b109041fdf62",
    ],
    [
      "param",
      "sort",
      "personalizedPagerank"
    ]
  ],
  "content": "",
  "sig": "be8b89b9db5f3579efe55417fbb76f626242936b3745aa0aaa67ed5a7e0107c7caa9a96bd1e78521528b642f240d972dcec88d6655992a80980a9acfd0c9ce72"
}
```

### Response

The response is a standard DVM response which includes the corresponding `e` and `p` tags. 
The `content` field is a JSON-stringified array of objects formatted as:

| Properties | Types | Description |
|-----|-----|-----|
| {`pubkey`, `rank`} | {string, float} | Requested target along with its rank |

Pubkeys are sorted in decreasing order by their ranks.

#### Example response

```json
{
  "id": "cc6a095e8e87977971806acea0670d92af3632da6242699c4a004ebad11b1347",
  "pubkey": "a9b008476119ea693cbd2f0b4de99fd346e2e30880b18d42234a1158bd323783",
  "created_at": 1732760389,
  "kind": 6314,
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
  "sig": "6fd60b9c07eac7b9150c25c4d5bb2652998b671b3b336c1407cac0473f90a25bfae5636a4eb27bcf40d2ba6f0b5f25e3300d3fdbae295dc9f2fc5cf74b793c11"
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
