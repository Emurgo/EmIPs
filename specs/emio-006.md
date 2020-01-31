# Abstract

This proposes a standard to annotate curation or reputation information to stake pools

# Motivation

Unlike Proof of Work where regular users have no say in who gets to make blocks, Proof of Stake empowers users of the protocol to influence who is creating blocks.

Users tend to choose who they delegate to based on two categories:

1) **Financial incentives** (who promises the highest reward)
2) **Moral incentives** (who is following the protocol, contributing to the community, etc.)

Given that

1) There are hundreds or thousands of pools to choose from
2) *Financial incentives* are very quick to compare
3) *Moral incentives* are very hard to compare

Most users will simply choose their pool based on the *financial incentive*.

This is problematic because:

1) This is sometimes detrimental to the protocol (an adversary could promise high returns but attack the protocol)
2) Empirically many users want to vote based on their morals but were not able to.

Therefore we need a way to simplify choosing based on **moral incentives** without resulting in centralization.

# Proposal

We propose to use a standard format for encoding pool curation information. A standard format allows us to decouple software creators from curators. For example, a wallet or explorer could bundle their software with a pre-existing set of curators but also allow users to override to use their own preferred curator.

### Format

We propose the format follows (defined using [JSON Schema](https://json-schema.org/)).

```
{
  "definitions": {
    "node_flags": {
      "$id": "#/definitions/node_flags",
      "type": "integer",
      "title": "Node-level Flags",
      "description": "Flags that can be set by node software indicating automatically-detected adversarial behavior",
      "default": 0
     }
  },
  "$schema": "http://json-schema.org/schema#",
  "type": "object",
  "patternProperties": {
    "^[a-f0-9]{64}$": {
      "type": "object",
      "properties": {
        "node_flags": { "$ref": "#/definitions/node_flags" }
      },
      "additionalProperties": false
    }
  },
  "additionalProperties": false
}
```

Here is an example

```json
{
  "0000111122223333444455556666777788889999aaaabbbbccccddddeeeeffff": {
    "node_flags": 1
  },
  "1000111122223333444455556666777788889999aaaabbbbccccddddeeeeffff": {
    "node_flags": 3
  },
}
```

Note: keys are *pool ids* (32-byte hex strings). Since keys are unique, it multiple curation systems can provide data for the same pools and the results can easily be merged together.

### Security considerations

The result of the endpoint should NOT be parsed as any kind of code that could be executed.

### Field registry

Here we provide an extensible list of keys used for this specification

##### node_flags

```json
"node_flags": {
  "$id": "#/definitions/node_flags",
  "type": "integer",
  "title": "Node-level Flags",
  "description": "Flags that can be set by node software indicating automatically-detected adversarial behavior",
  "default": 0
}
```

This is a bitset with the following table used for values

| value | meaning |
|---|---|
| 0 | no adversarial behavior detected (prefer to omit the field entirely in this case) |
| 1 | multiple blocks for a single slot |
| 2 | no transactions included in blocks |
