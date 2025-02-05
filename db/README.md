

# Kong with Database (Traditional)

https://docs.konghq.com/gateway/latest/production/deployment-topologies/traditional/

## Installation
```bash
docker compose up -d
```

After running local, will expose
- Postgresql Port 5433
- Kong Admin Port 8001
- Kong Proxy Port 8000


Upstream service test
```bash
https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1
```


## Usecase
1. Create service
2. Create route in service
3. Apply plugin Rate-Limit


### 1. Create service
```bash
curl -i -X POST 'http://localhost:8001/services' \
--header 'Content-Type: application/json' \
--data '{
    "name": "my-service",
    "retries": 5,
    "protocol": "https",
    "host": "api.coingecko.com",
    "port": 443,
    "path": "/",
    "connect_timeout": 6000,
    "write_timeout": 6000,
    "read_timeout": 6000,
    "tls_verify_depth": null,
    "enabled": true
  }'
```

Inquiry service
```bash
curl -i -X GET 'http://localhost:8001/services'
```


### 2. Create Route
```bash
curl -i -X POST 'http://localhost:8001/services/my-service/routes' \
--header 'Content-Type: application/json' \
--data '{
  "name": "my-route",
  "protocols": [
    "http",
    "https"
  ],
  "methods": [
    "GET"
  ],
  "paths": [
    "/api/v3/coins/markets"
  ],
  "https_redirect_status_code": 426,
  "regex_priority": 0,
  "strip_path": false,
  "path_handling": "v0",
  "preserve_host": false,
  "request_buffering": true,
  "response_buffering": true
}'
```

For Delete Service or Route
```bash
curl -i -X DELETE 'http://localhost:8001/services/{service_name}'
```

```bash
curl -i -X DELETE 'http://localhost:8001/routes/{route_name}'
```

### Let's try it
```bash
curl -i -X GET 'http://localhost:8000/api/v3/coins/markets'
```

### 3. Apply plugin Rate-Limit
https://docs.konghq.com/hub/kong-inc/rate-limiting/
```bash
curl -X POST http://localhost:8001/services/my-service/plugins \
   --header "accept: application/json" \
   --header "Content-Type: application/json" \
   --data '
   {
 "name": "rate-limiting",
 "config": {
   "minute": 5,
   "policy": "local"
 }
}
```