---
title: Search Profiles
weight: 40
---

This service provides a list of pubkeys matching the provided search term. [Try it live on npub.world](https://npub.world/).

Example use-cases:

 - Searching profiles
 - Auto-completion when tagging someone

## Kinds

 - Request: `5315`
 - Response: `6315`
 - Error: `7000`

## Request

### Parameters

All parameters must be passed as *strings* in the tags of the Request event, with the following format:

```json
["param", "<name>", "<value>"] 
```

| Param | Description | Default Value | Max |
|-----|-----|-----|-----|
| `search` _(required)_  | The search terms | - | one search |
| `sort` | Algorithm used to sort results | `"globalPagerank"` | one sort |
| `source` | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | one source |
| `limit` | Maximum number of results returned | `"5"` | `"100"` |

The `search` must be longer than three characters.  
Sorting algorithms can be found [here](/docs/algos).

### Example nak command
```
nak event --kind 5315 --tag param="search;jack" wss://relay.vertexlab.io
```

### Example request
```json
{
  "id": "7a0058...cff3e0",
  "pubkey": "79be66...f81798",
  "created_at": 1740754324,
  "kind": 5315,
  "tags": [
    [
      "param",
      "search",
      "jack",
    ],
  ],
  "content": "",
  "sig": "e64ce6...b25bd9"
}
```

## Response

### Tags

| Tag     | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `e`     | The event ID of the request                                                 |
| `p`     | The pubkey that signed the request                                          |
| `sort`  | The sorting algorithm specified in the request                              |
| `source`| The source specified in the request (present only if `sort="personalizedPagerank"`) |
| `nodes` | The number of nodes in the graph at the time the request was made           |

### Content

The `content` field is a JSON-stringified array of objects, each formatted as:

| Field | Type | Description |
|-----|-----|-----|
| `pubkey` | string | a nostr hex pubkey |
| `rank` | float | the rank computed with the `sort` algorithm |

Pubkeys are sorted in descending order by their `rank`.  
The `rank` is a combination of two components:
- `search_rank`: measures how well the search term matches the profile.
- `reputation_rank`: determined by the `sort` parameter.  

The combined rank is calculated with the formula:

```
rank = |search_rank|³ × reputation_rank
```

To learn more you can [check out our code](https://github.com/vertex-lab/relay/blob/master/pkg/dvm/response.go).

### Example nak command
```
nak req --kind 6315 --kind 7000 --tag e=7a00585895879e0c73d5e7db3364d66cc649a591aa422bfdfb14801642cff3e0 wss://relay.vertexlab.io
```

### Example response

```json
{
  "id": "6fa475...a0f9ed",
  "pubkey": "5fc48a...ccaeb4",
  "created_at": 1740754324,
  "kind": 6315,
  "tags": [
    [
      "e",
      "7a0058...cff3e0"
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
      "317238"
    ],
  ],
  "content":"[{\"pubkey\":\"82341f882b6eabcd2ba7f1ef90aad961cf074af15b9ef44a09f9d2a8fbfbe6a2\",\"rank\":8.224848486131593},{\"pubkey\":\"c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0\",\"rank\":2.120725804894317},{\"pubkey\":\"a1fc5dfd7ffcf563c89155b466751b580d115e136e2f8c90e8913385bbedb1cf\",\"rank\":0.2690585544819},{\"pubkey\":\"c5fb6ecc876e0458e3eca9918e370cbcd376901c58460512fe537a46e58c38bb\",\"rank\":0.14113029299502666},{\"pubkey\":\"5e5fc1434c928bcdcba6f801859d5238341093291980fd36e33b7416393d5a2c\",\"rank\":0.12754991708985827}]",
  "sig": "bc856a...898458"
}
```

Formatted `content` JSON:

```json
[
    {
      "pubkey": "82341f...fbe6a2",
      "rank": 8.224848486131593
    },
    {
      "pubkey": "c4eaba...cbc4e0",
      "rank": 2.120725804894317
    },
    {
      "pubkey": "a1fc5d...edb1cf",
      "rank": 0.2690585544819
    },
    {
      "pubkey": "c5fb6e...8c38bb",
      "rank": 0.14113029299502666
    },
    {
      "pubkey": "5e5fc1...3d5a2c",
      "rank": 0.12754991708985827
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
        "the search term must be longer than three characters"
      ],
  ],
  // ...
}
```

## Example code

### Go

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
	searchProfiles := &nostr.Event{
		Kind:      5315,
		CreatedAt: nostr.Now(),
		Tags: nostr.Tags{
			{"param", "search", "pip"},
			{"param", "limit", "7"},
		},
	}

	err = searchProfiles.Sign("YOUR_SECRET_KEY")
	if err != nil {
		panic(err)
	}

	// step 3: publish the request to the vertex relay
	err = relay.Publish(ctx, *searchProfiles)
	if err != nil {
		panic(err)
	}

	// step 3: fetch the response
	filter := nostr.Filter{
		Kinds: []int{6315, 7000},
		Tags: nostr.TagMap{
			"e": {searchProfiles.ID},
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

### Javascript

```javascript
import { Relay, finalizeEvent } from 'nostr-tools';

try {
  // step 1: connect to vertex
  const vertex = 'wss://relay.vertexlab.io';
  const relay = new Relay(vertex);
  await relay.connect();

  // step 2: generate the request and sign it with your vertex key.
  let searchProfiles = {
    kind: 5315,
    created_at: Math.floor(Date.now() / 1000),
    tags: [
      ['param', 'target', 'npub176p7sup477k5738qhxx0hk2n0cty2k5je5uvalzvkvwmw4tltmeqw7vgup'],
      ['param', 'limit', '7'],
    ],
    content: '',
  };

  searchProfiles = finalizeEvent(searchProfiles, 'YOUR_SECRET_KEY')

  // Step 3: publish the request
  await relay.publish(searchProfiles);

  // Step 4: fetch the response
  const filter = {
    kinds: [6315, 7000],
    '#e': [searchProfiles.id],
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