# about

Nginx reverse caching proxy + Elasticsearch

    http://git.io/vB5g7
    2015-11-30

    mkocikowski@gmail.com

# what I will do

- describe:
    - reverse proxy
    - how nginx handles proxy requests
    - sample project (uspto.io)
    - problem
    - solution

- in detail:
    - nginx config file example

# what is a reverse (caching) proxy

- sits between client and server:
    - ssl/tls termination, compression
    - protocol translation (http2/1.1/1)
    - load balancing (+health checks)
    - rate limiting
    - access control (nic, port, auth)
    - caching

# how nginx handles proxy requests

- client connects to nginx (http2/1.1/1)
- nginx reads *entire* request
- sends request to "upstream" (http1)
- reads *entire* response from "upstream"
- sends response to client

* default, can be changed

# my project (overview)

- http://uspto.io:
    - shows who owned what patents when
    - ~250GB of semi-structured data
    - 75ms 95 percentile for analytic queries
    - Google Cloud, Nginx, Elasticsearch, Go

# my project (architecture)

- client hits the REST API
- API queries elasticsearch
- API massages results, returns to client

# why (the problem, why am I doing this)

- I want things:
    - fast
    - cheap (anyone else?)
    - consistent / predictable response time
      (don't want to saturate backend; SLAs)

- analytic queries are expensive:
    - top terms
    - sig terms

# what (what did I do)

- rate limit elasticsearch requests:
    - maintain consistent performance
    - avoid duplicate effort
    - communicate via backpressure (503,504)

- cache elasticsearch responses:
    - data set well suited to caching
    - ...and so is the access pattern
    - abort on timeouts, continue on aborts

# how (how did I go about doing it)

- nginx between the API and elasticsearch:
    - `proxy_cache_key`
    - `proxy_cache_lock`
    - `proxy_ignore_client_abort`
    - `proxy_*_timeout`
    - `proxy_cache_bypass`

# example

- see nginx.conf ...

# thoughts

- caching:
    - is cheap
    - almost always helps (backend multiplier)
    - rarely hurts

- http comes with a ton of freebies
- keep the API simple, offload to nginx

- `nginx -g "daemon off;"`
- `nc -l 9200`

# I simplified; I actually have...

- 2 nginx proxies:
    - between client and API
    - between API and elasticsearch

- "overquery" elasticsearch:
    - request + cache large result set
    - filter down in API

# that's it!

- http://git.io/vB5g7
- I do consulting 
- mkocikowski@gmail.com

# the end


    ☁ ⚡ $

# the agile manifesto

Individuals and interactions 
    ...over processes and tools
Working software 
    ...over comprehensive documentation
Customer collaboration 
    ...over contract negotiation
Responding to change 
    ...over following a plan

