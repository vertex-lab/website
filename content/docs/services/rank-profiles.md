---
title: Rank Profiles
weight: 4
---

This service sorts a provided list of pubkeys using the specified algorithm.

Example use cases:
 - Batched ranking of pubkeys
 - Sorting comments under a note
 - Sorting zaps in a zap list

{{< callout type="info" >}}
Need more reputational metrics?  
Try [Verify Reputation](/docs/services/verify-reputation) for a more in-depth solution.
{{< /callout >}}

## Kinds

 - Request: `5314`
 - Response: `6314`
 - Error: `7000`

## Request

### Parameters

All parameters must be passed as *strings* in the tags of the Request event, with the following format:

```json
["param", "<name>", "<value>"] 
```

| Param | Description | Default Value | Max |
|-----|-----|-----|-----|
| `target`  _(required)_  | Pubkey whose ranking is being requested | - | 1000 targets |
| `sort` | Algorithm used to sort results | `"globalPagerank"` | one sort |
| `source` | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | one source |
| `limit` | Maximum number of results returned | `"5"` | `"1000"` |

Multiple `target` parameters should be supplied, as npubs or hex pubkeys.  
Sorting algorithms can be found [here](/docs/algos).

### Example nak command
```
nak event -k 5314 --tag param="target;726a1e261cc6474674e8285e3951b3bb139be9a773d1acf49dc868db861a1c11" --tag param="target;f683e87035f7ad4f44e0b98cfbd9537e16455a92cd38cefc4cb31db7557f5ef2" --tag param="target;d5ad3d3115d9fa07500b06ccd0b9605d9888a206acba20a1e2e681ec29109387" wss://relay.vertexlab.io
```

### Example request
```json
{
  "id": "1ded5c...b17980",
  "pubkey": "79be66...f81798",
  "created_at": 1740755732,
  "kind": 5314,
  "tags": [
    [
      "param",
      "target",
      "726a1e...1a1c11",
    ],
    [
      "param",
      "target",
      "f683e8...7f5ef2",
    ],
    [
      "param",
      "target",
      "d5ad3d...109387",
    ],
  ],
  "content": "",
  "sig": "177f42...2d2050"
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
| `rank` | float | the rank computed with the `sort` algorithm|

Pubkeys are sorted in descending order by their `rank`. 

### Example nak command
```
nak req -k 6314 -k 7000 --tag e=1ded5cc51ffc9a77b11e20bc9ed1c84ae3bdfa557b30dafc97e9982641b17980 wss://relay.vertexlab.io
```

### Example response

```json
{
  "id": "d7abd9...7e175c",
  "pubkey": "5fc48a...ccaeb4",
  "created_at": 1740755732,
  "kind": 6314,
  "tags": [
    [
      "e",
      "1ded5c...b17980"
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
		"pubkey": "726a1e...1a1c11",
		"rank": 00016816930295038342
	},
	{
		"pubkey": "f683e8...7f5ef2",
		"rank": 0.00008207271004088555
	},
	{
		"pubkey": "d5ad3d...109387",
		"rank": 0
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
	rankProfiles := &nostr.Event{
		Kind:      5314,
		CreatedAt: nostr.Now(),
		Tags: nostr.Tags{
			{"param", "target", "npub176p7sup477k5738qhxx0hk2n0cty2k5je5uvalzvkvwmw4tltmeqw7vgup"},
      {"param", "target", "npub12rv5lskctqxxs2c8rf2zlzc7xx3qpvzs3w4etgemauy9thegr43sf485vg"},
      {"param", "target", "npub1t3gd5yefglarhar4n6uh34uymvft4tgu8edk5465zzhtv4rrnd9sg7upxq"},
      {"param", "target", "npub1zuuajd7u3sx8xu92yav9jwxpr839cs0kc3q6t56vd5u9q033xmhsk6c2uc"},
		},
	}

	err = rankProfiles.Sign("YOUR_SECRET_KEY")
	if err != nil {
		panic(err)
	}

	// step 3: publish the request to the vertex relay
	err = relay.Publish(ctx, *rankProfiles)
	if err != nil {
		panic(err)
	}

	// step 3: fetch the response
	filter := nostr.Filter{
		Kinds: []int{6314, 7000},
		Tags: nostr.TagMap{
			"e": {rankProfiles.ID},
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
  let rankProfiles = {
    kind: 5314,
    created_at: Math.floor(Date.now() / 1000),
    tags: [
      ['param', 'target', 'npub176p7sup477k5738qhxx0hk2n0cty2k5je5uvalzvkvwmw4tltmeqw7vgup'],
      ['param', 'target', 'npub12rv5lskctqxxs2c8rf2zlzc7xx3qpvzs3w4etgemauy9thegr43sf485vg'],
      ['param', 'target', 'npub1t3gd5yefglarhar4n6uh34uymvft4tgu8edk5465zzhtv4rrnd9sg7upxq'],
      ['param', 'target', 'npub1zuuajd7u3sx8xu92yav9jwxpr839cs0kc3q6t56vd5u9q033xmhsk6c2uc'],
    ],
    content: '',
  };

  rankProfiles = finalizeEvent(rankProfiles, 'YOUR_SECRET_KEY')

  // Step 3: publish the request
  await relay.publish(rankProfiles);

  // Step 4: fetch the response
  const filter = {
    kinds: [6314, 7000],
    '#e': [rankProfiles.id],
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