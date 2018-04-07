# rate-limiter
[![Build Status](https://travis-ci.org/jlmurphysa/rate-limiter.svg?branch=master)](https://travis-ci.org/jlmurphysa/rate-limiter)
[![Coverage Status](https://coveralls.io/repos/github/jlmurphysa/rate-limiter/badge.svg?branch=master)](https://coveralls.io/github/jlmurphysa/rate-limiter?branch=master)
[![Standard Version](https://img.shields.io/npm/v/npm.svg)](https://img.shields.io/npm/v/npm.svg)

A powerful rate-limiter with annotation and channel support written for TypeScript.

## Features
* Annotation/decorator support with `@throttle`
* Implements the token-bucket algorithm for flexible rate options.
* Support for independent configurable `Channels` that are isolated from each other.

## Installation
`$ npm install @jlmurphysa/rate-limiter`

## Usage

### Simple default channel
```typescript
import { throttle, Channels } from '@jlmurphysa/rate-limiter'

class API {
    @throttle()
    static async request() {
        return 'OK';
    }
}
Channels.create(10, 'second')
```

### Simple named channels
```typescript
import { Channels, throttle } from '@jlmurphysa/rate-limiter'

class API {
    @throttle('google-places')
    static async requestToPlaces() {
        return 'OK';
    }

    @throttle('google-translate')
    static async requestToAnother() {

    }
}
Channels.create('google-places', 10, 'second')
Channels.create('google-translate', 20, 'hour')
```

### Advanced
```typescript
import {
    Channel,
    Channels,
    throttle
} from '@jlmurphysa/rate-limiter'

Channels.add('google-places', new Channel({
    interval: 1000,         // Token refresh interval
    bucketSize: 25,         // Max tokens the bucket will hold
    tokensPerInterval: 10,  // Number of tokens we receive per interval
    tokens: 25              // Initial tokens
})

const throttleOpts = {
    channel: 'google-places',
    cost: 5,    // The token cost of each call
    ttl: 5000   // Throw if we'll have to wait more than 5s.
}

class API {
    @throttle(throttleOpts)
    static async requestToPlaces() {
        return 'OK'
    }

    @throttle({...throttleOpts, cost: 8})
    static async requestToPlacesRich() {
        return 'OK!'
    }
}
```

## Future Work
+ **'priority' option** - not exactly trivial in the current implementation.
