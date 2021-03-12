# Prisma Vercel Deployment Example

This example shows how to deploy a REST API that uses Prisma to Vercel. The focus of this example is to show the considerations that take into account when deploying a Prisma based app to serverless environments.

## Load tests

The repository includes a load test script which can run with [k6](https://k6.io/docs/getting-started/installation). The load test has a configurable number of virtual users which represents the number of concurrent requests made by the load test.

The tests run at about ~120 requests per second in total (the exact number can be seen in the `http_reqs` row of the results)

To run load tests, install [k6](https://k6.io/docs/getting-started/installation) and run the following command:

```command
API_URL="https://your-app.vercel.app/api" k6 run loadtest.js
```

> **Note:** The `API_URl` should be the base API url of your Vercel deployment, e.g. `https://your-app.vercel.app/api`.

### Results

#### Test 1 - 40 virtual users | 11 connections pgbouncer pool

- Serverless deployment region: `Vercel` `lhr1` (`AWS eu-west-2`)
- Database deployment region: `Digital Ocean` `LON1`
- Database: PostgreSQL on Digital Ocean `1GB 1vCPU 10GB$15`
- Prisma `pgbouncer=true` mode enabled
- Connect to PgBouncer pool of `11` connections.
- No connection limit explicitly set in DB connection URL.

<details>
  <summary>Click to expand k6 results!</summary>

```
  execution: local
     script: loadtest.js
     output: -

scenarios: (100.00%) 1 scenario, 40 max VUs, 45s max duration (incl. graceful stop): \* default: 40 looping VUs for 15s (gracefulStop: 30s)

running (16.4s), 00/40 VUs, 378 complete and 0 interrupted iterations
default ✓ [======================================] 40 VUs 15s

    █ user flow

      ✓ status was 200 (get feed)
      ✓ status was 200 (add user)
      ✓ status was 200 (add post)
      ✓ status was 200 (add comment)
      ✓ status was 200 (add like)

    Create comment.............: avg=255.245196 min=145.152  med=236.331  max=1484.969 p(90)=300.6151 p(95)=366.40975
    Create like................: avg=257.040151 min=152.259  med=236.7385 max=1458.53  p(90)=295.7303 p(95)=332.19525
    Create post................: avg=216.826735 min=133.267  med=207.65   max=1272.74  p(90)=261.4987 p(95)=286.22635
    Create user................: avg=190.413169 min=117.838  med=184.4215 max=614.632  p(90)=240.0828 p(95)=259.9543
    Get feed...................: avg=218.637884 min=128.396  med=197.3495 max=1445.679 p(90)=305.8793 p(95)=350.8131
    checks.....................: 100.00% ✓ 1890 ✗ 0
    data_received..............: 2.9 MB  178 kB/s
    data_sent..................: 379 kB  23 kB/s
    group_duration.............: avg=1.66s      min=1.31s    med=1.58s    max=3.14s    p(90)=1.89s    p(95)=2.25s
    http_req_blocked...........: avg=4.32ms     min=0s       med=1µs      max=204.98ms p(90)=1µs      p(95)=1µs
    http_req_connecting........: avg=467.4µs    min=0s       med=0s       max=26.34ms  p(90)=0s       p(95)=0s
    http_req_duration..........: avg=227.63ms   min=117.83ms med=212.61ms max=1.48s    p(90)=283.5ms  p(95)=319.63ms
    http_req_receiving.........: avg=169.09µs   min=35µs     med=122µs    max=6.01ms   p(90)=297µs    p(95)=391µs
    http_req_sending...........: avg=111.43µs   min=22µs     med=97.5µs   max=1.01ms   p(90)=173µs    p(95)=195µs
    http_req_tls_handshaking...: avg=3.42ms     min=0s       med=0s       max=164.3ms  p(90)=0s       p(95)=0s
    http_req_waiting...........: avg=227.35ms   min=117.58ms med=212.34ms max=1.48s    p(90)=283.08ms p(95)=319.3ms
    http_reqs..................: 1890    114.920024/s
    iteration_duration.........: avg=1.66s      min=1.31s    med=1.58s    max=3.14s    p(90)=1.89s    p(95)=2.25s
    iterations.................: 378     22.984005/s
    vus........................: 13      min=13 max=40
    vus_max....................: 40      min=40 max=40
```

</details>

#### Test 2 - 40 virtual users | 22 connections pgbouncer pool

- Serverless deployment region: `Vercel` `lhr1` (`AWS eu-west-2`)
- Database deployment region: `Digital Ocean` `LON1`
- Database: PostgreSQL on Digital Ocean `1GB 1vCPU 10GB$15`
- Prisma `pgbouncer=true` mode enabled
- Connect to PgBouncer pool of `22` connections.
- No connection limit explicitly set in DB connection URL.

<details>
  <summary>Click to expand k6 results!</summary>

```

execution: local
script: loadtest.js
output: -

scenarios: (100.00%) 1 scenario, 40 max VUs, 45s max duration (incl. graceful stop): \* default: 40 looping VUs for 15s (gracefulStop: 30s)

running (16.4s), 00/40 VUs, 398 complete and 0 interrupted iterations
default ✓ [======================================] 40 VUs 15s

    █ user flow

      ✓ status was 200 (add post)
      ✓ status was 200 (add comment)
      ✓ status was 200 (add like)
      ✓ status was 200 (get feed)
      ✓ status was 200 (add user)

    Create comment.............: avg=228.28607  min=150.406  med=221.748  max=1220.4   p(90)=277.4572 p(95)=314.78225
    Create like................: avg=219.835553 min=150.418  med=216.1265 max=393.437  p(90)=263.1459 p(95)=298.71855
    Create post................: avg=217.272003 min=135.122  med=196.9875 max=1261.208 p(90)=256.9383 p(95)=288.8142
    Create user................: avg=180.754294 min=116.012  med=176.294  max=341.534  p(90)=232.1042 p(95)=260.69095
    Get feed...................: avg=202.719274 min=121.296  med=186.02   max=1211.562 p(90)=274.6679 p(95)=318.208
    checks.....................: 100.00% ✓ 1990 ✗ 0
    data_received..............: 3.1 MB  188 kB/s
    data_sent..................: 398 kB  24 kB/s
    group_duration.............: avg=1.57s      min=1.22s    med=1.51s    max=2.97s    p(90)=1.77s    p(95)=1.92s
    http_req_blocked...........: avg=4.64ms     min=0s       med=1µs      max=231.81ms p(90)=1µs      p(95)=1µs
    http_req_connecting........: avg=935.71µs   min=0s       med=0s       max=53.87ms  p(90)=0s       p(95)=0s
    http_req_duration..........: avg=209.77ms   min=116.01ms med=198.83ms max=1.26s    p(90)=263.85ms p(95)=298.4ms
    http_req_receiving.........: avg=1.43ms     min=33µs     med=275µs    max=34.95ms  p(90)=4.27ms   p(95)=6.14ms
    http_req_sending...........: avg=100.52µs   min=21µs     med=90µs     max=491µs    p(90)=166µs    p(95)=185µs
    http_req_tls_handshaking...: avg=3.67ms     min=0s       med=0s       max=188.71ms p(90)=0s       p(95)=0s
    http_req_waiting...........: avg=208.23ms   min=113.75ms med=197.41ms max=1.26s    p(90)=262.74ms p(95)=293.31ms
    http_reqs..................: 1990    121.46971/s
    iteration_duration.........: avg=1.57s      min=1.22s    med=1.51s    max=2.97s    p(90)=1.77s    p(95)=1.92s
    iterations.................: 398     24.293942/s
    vus........................: 10      min=10 max=40
    vus_max....................: 40      min=40 max=40

```

</details>

#### Test 3 - 40 virtual users | 22 connection pgbouncer pool | connection limit 1

- Serverless deployment region: `Vercel` `lhr1` (`AWS eu-west-2`)
- Database deployment region: `Digital Ocean` `LON1`
- Database: PostgreSQL on Digital Ocean `1GB 1vCPU 10GB$15`
- Prisma `pgbouncer=true` mode enabled
- Connect to PgBouncer pool of `22` connections.
- `connection_limit` of serverless function set to `1`

<details>
  <summary>Click to expand k6 results!</summary>

```

execution: local
script: loadtest.js
output: -

scenarios: (100.00%) 1 scenario, 40 max VUs, 45s max duration (incl. graceful stop): \* default: 40 looping VUs for 15s (gracefulStop: 30s)

running (16.5s), 00/40 VUs, 406 complete and 0 interrupted iterations
default ✓ [======================================] 40 VUs 15s

    █ user flow

      ✓ status was 200 (add user)
      ✓ status was 200 (add post)
      ✓ status was 200 (add comment)
      ✓ status was 200 (add like)
      ✓ status was 200 (get feed)

    Create comment.............: avg=226.669611 min=151.228  med=220.1025 max=480.728  p(90)=274.1325 p(95)=300.08675
    Create like................: avg=226.820709 min=152.731  med=223.0025 max=441.357  p(90)=278.0835 p(95)=312.7395
    Create post................: avg=201.404271 min=135.677  med=197.821  max=447.96   p(90)=244.9295 p(95)=269.17925
    Create user................: avg=172.929461 min=123.331  med=168.3885 max=412.627  p(90)=207.5325 p(95)=229.3135
    Get feed...................: avg=202.261429 min=120.83   med=184.9705 max=652.92   p(90)=279.137  p(95)=336.94375
    checks.....................: 100.00% ✓ 2030 ✗ 0
    data_received..............: 3.1 MB  190 kB/s
    data_sent..................: 405 kB  24 kB/s
    group_duration.............: avg=1.55s      min=1.3s     med=1.51s    max=2.23s    p(90)=1.81s    p(95)=1.91s
    http_req_blocked...........: avg=4.11ms     min=0s       med=1µs      max=210.24ms p(90)=1µs      p(95)=1µs
    http_req_connecting........: avg=472.12µs   min=0s       med=0s       max=26.04ms  p(90)=0s       p(95)=0s
    http_req_duration..........: avg=206.01ms   min=120.83ms med=197.59ms max=652.92ms p(90)=263.63ms p(95)=298.5ms
    http_req_receiving.........: avg=1.39ms     min=29µs     med=358µs    max=14.85ms  p(90)=4.21ms   p(95)=5.58ms
    http_req_sending...........: avg=116.06µs   min=20µs     med=105µs    max=399µs    p(90)=182µs    p(95)=210µs
    http_req_tls_handshaking...: avg=3.22ms     min=0s       med=0s       max=168.52ms p(90)=0s       p(95)=0s
    http_req_waiting...........: avg=204.5ms    min=120.62ms med=196.04ms max=652.64ms p(90)=261.35ms p(95)=297.73ms
    http_reqs..................: 2030    122.754114/s
    iteration_duration.........: avg=1.55s      min=1.3s     med=1.51s    max=2.23s    p(90)=1.81s    p(95)=1.91s
    iterations.................: 406     24.550823/s
    vus........................: 14      min=14 max=40
    vus_max....................: 40      min=40 max=40

```

</details>


#### Test 4 - 40 virtual users | 22 connection pgbouncer pool | connection limit 1 | Cold start

- Serverless deployment region: `Vercel` `lhr1` (`AWS eu-west-2`)
- Database deployment region: `Digital Ocean` `LON1`
- Database: PostgreSQL on Digital Ocean `1GB 1vCPU 10GB$15`
- Prisma `pgbouncer=true` mode enabled
- Connect to PgBouncer pool of `22` connections.
- `connection_limit` of serverless function set to `1`
- To ensure that there are no cold-starts, the load test was run after a new deployment.

<details>
  <summary>Click to expand k6 results!</summary>

```
  execution: local
     script: loadtest.js
     output: -

  scenarios: (100.00%) 1 scenario, 40 max VUs, 45s max duration (incl. graceful stop):
           * default: 40 looping VUs for 15s (gracefulStop: 30s)


running (17.5s), 00/40 VUs, 40 complete and 0 interrupted iterations
default ✓ [======================================] 40 VUs  15s

    █ user flow

      ✓ status was 200 (add like)
      ✗ status was 200 (get feed)
       ↳  97% — ✓ 39 / ✗ 1
      ✓ status was 200 (add user)
      ✓ status was 200 (add post)
      ✓ status was 200 (add comment)

    Create comment.............: avg=2.9s     min=1.36s    med=2.91s   max=4.81s    p(90)=4.18s    p(95)=4.29s
    Create like................: avg=2.73s    min=1.3s     med=2.91s   max=4s       p(90)=3.73s    p(95)=3.82s
    Create post................: avg=2.25s    min=144.41ms med=2.39s   max=4.6s     p(90)=4.52s    p(95)=4.55s
    Create user................: avg=3.14s    min=1.2s     med=2.44s   max=5.8s     p(90)=5.75s    p(95)=5.76s
    Get feed...................: avg=4.94s    min=3.62s    med=4.37s   max=6.24s    p(90)=6.21s    p(95)=6.22s
    checks.....................: 99.50% ✓ 199  ✗ 1
    data_received..............: 464 kB 26 kB/s
    data_sent..................: 63 kB  3.6 kB/s
    group_duration.............: avg=16.74s   min=16.21s   med=16.73s  max=17.49s   p(90)=17.44s   p(95)=17.45s
    http_req_blocked...........: avg=51.46ms  min=0s       med=1µs     max=258.53ms p(90)=257.21ms p(95)=257.84ms
    http_req_connecting........: avg=4.75ms   min=0s       med=0s      max=30.96ms  p(90)=22.35ms  p(95)=25.85ms
    http_req_duration..........: avg=3.19s    min=144.41ms med=2.95s   max=6.24s    p(90)=5.69s    p(95)=6.13s
    http_req_receiving.........: avg=172.16µs min=45µs     med=134.5µs max=1.68ms   p(90)=272.2µs  p(95)=330.64µs
    http_req_sending...........: avg=137.93µs min=53µs     med=120.5µs max=388µs    p(90)=217.1µs  p(95)=255µs
    http_req_tls_handshaking...: avg=41.12ms  min=0s       med=0s      max=209.57ms p(90)=207.07ms p(95)=208.34ms
    http_req_waiting...........: avg=3.19s    min=144.08ms med=2.95s   max=6.24s    p(90)=5.69s    p(95)=6.13s
    http_reqs..................: 200    11.419619/s
    iteration_duration.........: avg=16.74s   min=16.21s   med=16.73s  max=17.49s   p(90)=17.44s   p(95)=17.45s
    iterations.................: 40     2.283924/s
    vus........................: 6      min=6  max=40
    vus_max....................: 40     min=40 max=40
```

</details>