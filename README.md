# eowyn

Embedded-Only Webserver for C++ & Linux & x86-64. Fastest possible.

In progress.

## Concept

***Eowyn*** web server integrates with embedding web application as tight as possible, aiming to completely eliminate perfomance and memory overhead of communication between server and application, including overhead of converting request data between different representations used by server and application.

This is acheived by extensive use of code generation. Application developer provides metadata which contains URI-to-handler map, request headers and parameter structures accepted by various URI handlers, etc. Then during application build process, `eowyn-buildtime` uses that metadata to generate parts of `eowyn-runtime`: runtime structures, header and parameter parsers, etc. Generated parsers are LL(1): no table-drivens, no regexps, no compromises, only ultimate speed with some `x86-64`-specific optimizations.

During request processing, ***eowyn*** parses request headers and parameters in single pass directly into generated structures, compact and convenient. For example, `Content-Length` is stored as integer; dates (like `If-Modified-Since`) are stored as integer timestamps. Multi-level parameter structures and arrays are supported. User-defined headers and parameter types are supported.

Keen knowledge of application's needs also allows to significantly optimize some server internals.

Obviously, ***eowyn*** cannot be used as proxy server: since she parses requests into hard-coded application-defined structures, she cannot accept and pass through arbitrary requests. So she can operate only as end-station in HTTP request processing.

## Status

### What's done

HTTP/1.1 works, but misses some important features: SSL, gzip/deflate, serving files from filesystem (currently serves only files compiled by `eowyn-buildtime` into application as resources); multipart requests support is incomplete.

Simplest "Hello world" HTTP/1.1 application is 15-20% faster than same [nginx](https://nginx.org/) 1.12.2 [module](https://github.com/perusio/nginx-hello-world-module) and is much more stable under extremely high load (meaning, nginx tends to drop connections). Tested with Ryzen 7 1800X, socket sharding mode, same number of workers and max connections, logging turned off. Both ***eowyn*** and nginx spend the very most of their time in kernel mode. That is, in userspace ***eowyn*** is times faster than nginx, but linux kernel (epoll API) is the bottleneck which eliminates most of the gain. Still, the larger request - the more efficient ***eowyn*** will be compared to nginx, because the whole idea of ***eowyn*** is about efficient request parsing.

Few heavily commented usage examples are ready; not sure if should publish them here, because they are HTTP/1.1-only.

### What's in progress

Currently developing HTTP/2 support, because "fastest possible webserver without HTTP/2 support" sounds stupid. SSL support will come along as it's necessary for protocol negotiation. Parameter validators ifrastructure is also implemented in this branch.

### Legal

Currently closed-source; only documentation and examples (sources and binaries) will be published here. Once HTTP/2 support is complete, I plan to either sell it or go for crowdfunding and open-source.

-- Dmitry Grigoriev, [mail@dimgel.ru](mailto:mail@dimgel.ru)
