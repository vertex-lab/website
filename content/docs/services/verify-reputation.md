---
title: Verify Reputation
weight: 2
---

This service returns reputation information about a pubkey, including a list of pubkeys who follow it sorted by an algorithm. [Try it live on npub.world](https://npub.world/npub1kpt95rv4q3mcz8e4lamwtxq7men6jprf49l7asfac9lnv2gda0lqdknhmz).

Example use cases:

  - Helping users assess the reputation of a npub
  - Showing relevant followers of that npub
  - Reducing the probability of successful impersonators

{{< callout type="info" >}}
Need the ranks of many pubkeys?  
Try [Rank Profiles](/docs/services/rank-profiles) for a fast, batched solution.
{{< /callout >}}

## Kinds

 - Request: `5312`
 - Response: `6312`
 - Error: `7000`

## Request

### Parameters

All parameters must be passed as *strings* in the tags of the Request event, with the following format:

```json
["param", "<name>", "<value>"] 
```

| Param | Description | Default Value | Max |
|-----|-----|-----| ----- |
| `target` _(required)_  | Pubkey whose reputation is being requested | - | one target |
| `sort` | Algorithm used to sort results | `"globalPagerank"` | one sort |
| `source` | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | one source |
| `limit` | Maximum number of results returned | `"5"` | `"100"` |

Pubkeys can be in either hex or npub format.  
Sorting algorithms can be found [here](/docs/algos).

### Example nak command
```
nak event -k 5312 --tag param="target;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" wss://relay.vertexlab.io
```

### Example request

```json
{
  "id": "56729c...8fcb0c",
  "pubkey": "79be66...f81798",
  "created_at": 1745933480,
  "kind": 5312,
  "tags": [
    [
      "param",
      "target",
      "726a1e...1a1c11"
    ],
  ],
  "content": "",
  "sig": "ba113d...8a4112"
}
```

## Response

### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `sort`  | The sorting algorithm specified in the request                              |
| `source`| The source specified in the request (present only if `sort=personalizedPagerank`) |
| `nodes` | The number of nodes in the graph at the time the request was made           |

### Content

The `content` field is a JSON-stringified array of objects, each formatted as:

| Field | Type | Description |
|-----|-----|-----|
| `pubkey` | string | a nostr hex pubkey|
| `rank` | float | the rank computed with the `sort` algorithm |


The first object always contains the `pubkey` and `rank` of the `target`, as well as its `follows` and `followers` counts (`int`).

The subsequent pairs are the `pubkey`s and `rank`s of the top followers of `target`, sorted in descending order by their `rank`.

### Example nak command
```
nak req -k 6312 -k 7000 --tag e=56729c03239d0d80d0641caa4561c55a061cd2f30f1fe017efe712b37e8fcb0c wss://relay.vertexlab.io
```

### Example response

```json
{
  "id": "c9670c7cb3ed65bdd10319ebf31f1905007965226b97e3b5cd2d61b528b575af",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1745933480,
  "kind": 6312,
  "tags": [
    [
      "e",
      "56729c...8fcb0c"
    ],
    [
      "p",
      "79be66...f81798"
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
		"pubkey": "726a1e...1a1c11",
		"rank": 0.00018008434745346786,
		"follows": 550,
		"followers": 2102
	},
	{
		"pubkey": "32e182...68e245",
		"rank": 0.0038190735362639222
	},
	{
		"pubkey": "04c915...fbecc9",
		"rank": 0.0023781006037430445
	},
	{
		"pubkey": "3bf0c6...fa459d",
		"rank": 0.0020976712437000707
	},
	{
		"pubkey": "472f44...69301e",
		"rank": 0.0014937791227049887
	},
	{
		"pubkey": "3f770d...745b24",
		"rank": 0.0014543235565134729
	},
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
        "invalid target: this is not a valid public key"
      ],
  ],
  // ...
}
```

## Example code

{{< tabs items="Go,Javascript" >}}

{{< tab >}}
```golang
package main

import (
  "context"
  "fmt"

  "github.com/nbd-wtf/go-nostr"
)

func main() {
  // step 1: connect to vertex
  vertex := "wss://relay.vertexlab.io"
  relay, err := nostr.RelayConnect(context.Background(), vertex)
  if err != nil {
    panic(err)
  }

  // step 2: generate the request and sign it with your vertex key.
  // Be careful to store your key securely.
  verifyReputation := &nostr.Event{
    Kind:      5312,
    CreatedAt: nostr.Now(),
    Tags: nostr.Tags{
      {"param", "target", "npub176p7sup477k5738qhxx0hk2n0cty2k5je5uvalzvkvwmw4tltmeqw7vgup"},
      {"param", "limit", "7"},
    },
  }

  err = verifyReputation.Sign("YOUR_SECRET_KEY")
  if err != nil {
    panic(err)
  }

  // step 3: publish the request to the vertex relay
  err = relay.Publish(ctx, *verifyReputation)
  if err != nil {
    panic(err)
  }

  // step 3: fetch the response
  filter := nostr.Filter{
    Kinds: []int{6312, 7000},
    Tags: nostr.TagMap{
      "e": {verifyReputation.ID},
    },
  }

  responses, err := relay.QueryEvents(ctx, filter)
  if err != nil {
    panic(err)
  }

  // extract the first response
  response := <-responses

  // step 4: use the response in your app
  fmt.Printf("response: %v\n", response)
}
```
{{< /tab >}}

{{< tab >}}
```javascript
import { Relay, finalizeEvent } from 'nostr-tools';

try {
  // step 1: connect to vertex
  const vertex = 'wss://relay.vertexlab.io';
  const relay = new Relay(vertex);
  await relay.connect();

  // step 2: generate the request and sign it with your vertex key.
  let verifyReputation = {
    kind: 5312,
    created_at: Math.floor(Date.now() / 1000),
    tags: [
      ['param', 'target', 'npub176p7sup477k5738qhxx0hk2n0cty2k5je5uvalzvkvwmw4tltmeqw7vgup'],
      ['param', 'limit', '7'],
    ],
    content: '',
  };

  verifyReputation = finalizeEvent(verifyReputation, 'YOUR_SECRET_KEY')

  // Step 3: publish the request
  await relay.publish(verifyReputation);

  // Step 4: fetch the response
  const filter = {
    kinds: [6312, 7000],
    '#e': [verifyReputation.id],
  };

  const sub = relay.subscribe([filter], {
      onevent(response) {
        // step 5: use it in your app
          console.log('response:', response);
      },
      oneose() {
        resolve(events);
        sub.close();
      },
  })

} catch(err) {
  console.log('error:', err)
}
```
{{< /tab >}}

{{< /tabs >}}