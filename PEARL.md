# Pearl Stratum Protocol

Stratum is a newline-delimited JSON over TCP. `C` = miner, `S` = pool; `<...>` = placeholder:

## Quick start

```text
C | {"id":1,"method":"mining.authorize","params":{"wallet":"<address>/rig1","agent":"example/1.0","password":"d=2097152","type":"v2","devices":["NVIDIA RTX 5090"]}}
S | {"id":1,"result":true,"error":null,"type":"v2"}
S | {"id":null,"method":"mining.notify","params":{"header":"<76-byte header hex>","height":100000,"job_id":"1234abcd_2097152","target":"<32-byte target hex>","cert_version":3}}
C | {"id":2,"method":"mining.submit","params":{"job_id":"1234abcd_2097152","plain_proof":"<base64(gzip(proof bytes))>"}}
S | {"id":2,"result":true,"error":null}
```

Client speaks first. Pool pushes jobs via `mining.notify`.

## Difficulty

Kryptex Pool allows miners to set a custom difficulty. 

When:

- your workers have a high hashrate (e.g. more than 0.5-1.0 PH/s per worker)
- you aggregate a lot of hashrate through a proxy server

Why:

- Sending more than 10 shares per minute is meaningless
- Sending too many low-difficulty shares can get you rate-limited or reduce your effective hashrate by a few percent because of the network and CPU overhead
- This will reduce the amount of traffic and the load both for you and for the pool, especially on bad connections

How:

- Set `password` to `d=<integer>`
- Default/minimum: `2097152` ≈ 9007 TH per share ≈ 1 share every 90 seconds with 100 TH/s. 

To pick the perfect d= value, use the following formula:
```
d = your hashrate (H/s) × target share time (seconds) / 4,294,967,296
```

For example, if your hashrate is 50 PH/s and you want to send 1 share every 30 seconds, set the field to:
```
"password":"d=349245965"
```
