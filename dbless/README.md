

# Kong with Database (DB-Less)

https://docs.konghq.com/gateway/latest/production/deployment-topologies/db-less-and-declarative-config/

## Installation
```bash
docker compose up -d
```

After running local, will expose
- Kong Admin Port 8001 (In DB-Less mode can only use Read api)
- Kong Proxy Port 8000


Upstream service test
```bash
https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1
```


## Usecase
1. Create service
2. Create route in service
3. Apply plugin Key-auth

### Let's try it
```bash
curl -i -X GET 'http://localhost:8000/api/v3/coins/markets'
```