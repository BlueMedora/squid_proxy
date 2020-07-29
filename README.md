# Usage

## SSL Bump (intercept)

Use this mode when you want the proxy to present its own
certificate to the client, acting as a "man in the middle".

### run image
```
docker run \
  -p 3128:3128 \
  bmedora/squid:0.1.1 \
  /apps/squid/sbin/squid:0.1.1 -NsY -f /apps/squid.conf.intercept
```

### test with curl (no verify)
```
curl -v -k -x localhost:3128 -L https://google.com
```

### test with curl (verify)

Curl on macos seems to have trouble with the --cacert flag, you
can fix it with
```
brew install curl && brew link curl --force
```

test (without `-k`)
```
curl -vvv \
  --proxy-cacert CA_crt.pem \
  --cacert CA_crt.pem \
  -x localhost:3128 \
  -L https://google.com
```

## SSL Bump w/ Basic Auth (intercept)

Same configuration as SSL Bump, but with basic auth required.

### run image
```
docker run \
  -p 3128:3128 \
  bmedora/squid:0.1.1 \
  /apps/squid/sbin/squid:0.1.1 -NsY -f /apps/squid.conf.intercept_basicauth
```

### test with curl (no verify)
```
curl -v -k -x localhost:3128 \
  --proxy-user user1:user1 \
  -L https://google.com
```

### test with curl (verify)

Curl on macos seems to have trouble with the --cacert flag, you
can fix it with
```
brew install curl && brew link curl --force
```

test (without `-k`)
```
curl -vvv \
  --proxy-cacert CA_crt.pem \
  --cacert CA_crt.pem \
  -x localhost:3128 \
  --proxy-user user1:user1 \
  -L https://google.com
```
