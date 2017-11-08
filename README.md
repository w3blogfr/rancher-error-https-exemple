This repo is just to reproduce an issue when we issue traefik as front of rancher

run docker-compose.


Once loaded, go to https://localhost, accept self-signed certificate


It's doesn't matter if you don't have host.

Open chrome console.

Go to stacks > Infrastructure

Select healthcheck

Start to start/stop/restart service.

first time, it's could work. 
After few seconds, you will have

fetch.js:404 Mixed Content: The page at 'https://localhost/env/1a5/apps/stacks/1st1?which=infra' was loaded over HTTPS, but requested an insecure XMLHttpRequest endpoint 'http://localhost/v2-beta/projects/1a5/services/1s1/?action=deactivate'. This request has been blocked; the content must be served over HTTPS.


## Solution

I found a solution by upgrading traefik to 1.4.2 to use custom header

```
[file]

# rules
[backends]
  [backends.rancher]
    [backends.rancher.servers.server1]
    url = "http://rancher:8080"

[frontends]
  [frontends.rancher]
  backend = "rancher"
  passHostHeader = true
    [frontends.rancher.headers.customrequestheaders]
    X-Forwarded-Proto = "https"
    [frontends.rancher.routes.test_1]
    rule = "Host:localhost"
```

For the moment, traefik docker provider do not support customrequestheaders as label, so we need to back to a File Provider.

Seem that label provider support will be solve with next version 1.5 (https://github.com/containous/traefik/issues/2028)
