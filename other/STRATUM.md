# Stratum Mining Protocol

This is the description of stratum protocol used in this pool.

Stratum defines simple exception handling. Example of rejected share looks like:

```javascript
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: 23, message: "Invalid share" } }
```

Each response with exception is followed by disconnect.

## Authentication

Request looks like:

```javascript
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "login",
  "params": ["0xb85150eb365e7df0941f0cf08235f987ba91506a"]
}
```

Request can include additional 2nd param (email for example):

```javascript
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "login",
  "params": ["0x00000000000000000000000000000000000000000", "admin@example.net"]
  "worker": "0001"
}
```

Successful response:

```javascript
{ "id": 1, "jsonrpc": "2.0", "result": true }
```

Exceptions:

```javascript
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: -1, message: "Invalid login" } }
```

## Request For Job

Request looks like:

```javascript
{ "id": 1, "jsonrpc": "2.0", "method": "getwork" }
```

Successful response:

```javascript
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
      "0x1000",//JobId
      "0x1000"//Version
      "0x1000" //Height
      "0x36daa8e713e4a06fec0d8187c6b44004b4772b61613be2cba597ff53a7f386b9", //PreviousBlockHash
      "0x000000000", //Timestamp
      "0x37096bd7e848bd899543db164bebd55677d903e646f8feb2cdf266819cf0f663", //TransactionsMerkleRoot
      "0x0000000000000000000000000000000000000000000000000000000000000000", //TransactionStatusHash
      "0x800000000", //Nonce
      "0x000000000" //Bits
    ]
}
```

Exceptions:

```javascript
{ "id": 10, "result": null, "error": { code: 0, message: "Work not ready" } }
```

## New Job Notification

Server sends job to peers if new job is available:

```javascript
{
  "jsonrpc": "2.0",
  "result": [
       "0x1000",//JobId
       "0x1000"//Version
       "0x1000" //Height
       "0x36daa8e713e4a06fec0d8187c6b44004b4772b61613be2cba597ff53a7f386b9", //PreviousBlockHash
       "0x000000000", //Timestamp
       "0x37096bd7e848bd899543db164bebd55677d903e646f8feb2cdf266819cf0f663", //TransactionsMerkleRoot
       "0x0000000000000000000000000000000000000000000000000000000000000000", //TransactionStatusHash
       "0x800000000", //Nonce
       "0x000000000" //Bits
    ]
}
```

## Share Submission

Request looks like:

```javascript
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "submit",
  "params": [
    "0x1", //job_id
    "0x800000000" //nonce
  ]
}
```

Request can include optional `worker` param:

```javascript
{ "id": 1, "worker": "rig-1" /* ... */ }
```

Response:

```javascript
{ "id": 1, "jsonrpc": "2.0", "result": true }
{ "id": 1, "jsonrpc": "2.0", "result": false }
```

Exceptions:

Pool MAY return exception on invalid share submission usually followed by temporal ban.

```javascript
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: 23, message: "Invalid share" } }
```

```javascript
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: 22, message: "Duplicate share" } }
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: -1, message: "High rate of invalid shares" } }
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: 25, message: "Not subscribed" } }
{ "id": 1, "jsonrpc": "2.0", "result": null, "error": { code: -1, message: "Malformed PoW result" } }
```
