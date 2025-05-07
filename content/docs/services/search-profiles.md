---
title: Search Profiles
weight: 40
---

This service provides a list of pubkeys matching the provided search term. [Try it live on npub.world](https://npub.world/).

Example use-cases:

 - Searching profiles
 - Auto-completion when tagging someone

### Kinds

 - Request: 5315
 - Response: 6315
 - Error: 7000

### Request parameters

| Param | Type | Description | Default Value | Max |
|-----|-----|-----|-----|-----|
| `search` _(required)_  | string | The search terms | - | 1 |
| `sort` | string | Algorithm used to sort results | `globalPagerank` | 1 |
| `source` | string | Pubkey that provides the "point of view" for personalized algorithms | The pubkey signing the DVM request | 1 |
| `limit` | int | Maximum number of results returned | `5` | `100` |

The `search` must be longer than three characters.  
Sorting algorithms can be found [here](/docs/algos).

### Request

#### Example nak command
```
nak event --kind 5315 --tag param="search;jack" wss://relay.vertexlab.io
```

#### Example request event
```json
{
  "id": "7a00585895879e0c73d5e7db3364d66cc649a591aa422bfdfb14801642cff3e0",
  "pubkey": "79be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798",
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
  "sig": "e64ce664130301b86e055acc3fa3fb15fce058ff26bd156b80d4b7be2016d6baa49c21fa6f5b2481bbaaf66ac1f61cdd75bf4a9db175efb193bb72f45eb25bd9"
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

#### Example nak command
```
nak req --kind 6315 --kind 7000 --tag e=7a00585895879e0c73d5e7db3364d66cc649a591aa422bfdfb14801642cff3e0 wss://relay.vertexlab.io
```

#### Example response event

```json
{
  "id": "6fa47583e8e3c9d54de7d163b6b9f1d2ddc959c354979a3fe12bb3df07a0f9ed",
  "pubkey": "5fc48ac4765ff81e9c51014b9d2f2c91621370f4c6b5452a9c06456e4cccaeb4",
  "created_at": 1740754324,
  "kind": 6315,
  "tags": [
    [
      "e",
      "7a00585895879e0c73d5e7db3364d66cc649a591aa422bfdfb14801642cff3e0"
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
      "317238"
    ],
  ],
  "content":"[{\"pubkey\":\"82341f882b6eabcd2ba7f1ef90aad961cf074af15b9ef44a09f9d2a8fbfbe6a2\",\"rank\":8.224848486131593},{\"pubkey\":\"c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0\",\"rank\":2.120725804894317},{\"pubkey\":\"a1fc5dfd7ffcf563c89155b466751b580d115e136e2f8c90e8913385bbedb1cf\",\"rank\":0.2690585544819},{\"pubkey\":\"c5fb6ecc876e0458e3eca9918e370cbcd376901c58460512fe537a46e58c38bb\",\"rank\":0.14113029299502666},{\"pubkey\":\"5e5fc1434c928bcdcba6f801859d5238341093291980fd36e33b7416393d5a2c\",\"rank\":0.12754991708985827}]",
  "sig": "bc856a8a93bc24a784ee53245fd6a99dfbd867f5c9f801b09ed499239d9e80c1390470f83882de169b1a30b40deb687349f4d671329fd12660132fedcf898458"
}
```

Formatted `content` JSON:

```json
[
    {
      "pubkey": "82341f882b6eabcd2ba7f1ef90aad961cf074af15b9ef44a09f9d2a8fbfbe6a2",
      "rank": 8.224848486131593
    },
    {
      "pubkey": "c4eabae1be3cf657bc1855ee05e69de9f059cb7a059227168b80b89761cbc4e0",
      "rank": 2.120725804894317
    },
    {
      "pubkey": "a1fc5dfd7ffcf563c89155b466751b580d115e136e2f8c90e8913385bbedb1cf",
      "rank": 0.2690585544819
    },
    {
      "pubkey": "c5fb6ecc876e0458e3eca9918e370cbcd376901c58460512fe537a46e58c38bb",
      "rank": 0.14113029299502666
    },
    {
      "pubkey": "5e5fc1434c928bcdcba6f801859d5238341093291980fd36e33b7416393d5a2c",
      "rank": 0.12754991708985827
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
        "invalid search: the search term must be longer than three characters"
      ],
  ],
  "content": "",
  "sig": "6318c7ce6c57dd85779faadc4fed7733a1e50bd46205d52c96e535648dd2dd07e573e8d1fdef496d27014c80d1f2f604cb5337744d24000c00dde485ccdcf48c"
}
```
