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

## HTTP ssl bump, HTTPS, basic auth

- http port performs ssl bump, certificate replacement on the fly
- https port does NOT perform ssl bump, TCP socket is TLS secured

### run image
```
docker run \
  -p 3128:3128 \
  -p 3129:3129 test \
  /apps/squid/sbin/squid -NY -f /apps/squid.conf.intercept_basicauth_tls
```

### test with curl (verify ssl)

set environment
```
export HTTP_PROXY=http://localhost:3128
export HTTPS_PROXY=https://localhost:3129
```

Test http w/ ssl bump. The example site is specifically chosen because it
does not use HTTPS, and will not attempt to redirect to HTTPS. (7/31/2020)
```
curl -vvv \
  --proxy-cacert CA_crt.pem \
  --cacert CA_crt.pem  \
  -L http://www.pootypaintball.com/ \
  --proxy-user user1:user1 > /dev/null
```

Test https (squid port is listening with TLS)
```
openssl s_client -connect squid-bump.bluemedora.localnet:3129

curl -vvv \
  --proxy-cacert CA_crt.pem \
  --cacert CA_crt.pem  \
  -L https://google.com// \
  --proxy-user user1:user1 > /dev/null
```
