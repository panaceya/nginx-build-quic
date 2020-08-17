# About
This project as my test and examples how to build [nginx] with [QUIC] support.

# Requirements
* Docker

# Usage
[All tags](https://hub.docker.com/r/panaceya/nginx-build-quic/tags)


`docket pull panaceya/nginx-build-quic:tag` where `tag` is one of:

* ubuntu-18.04
* ubuntu-20.04
* centos-7
* centos-8

## Docker file
```
FROM panaceya/nginx-build-quic:<tag>
#
# some stages
#

# start Nginx
CMD ["nginx"]
```




# Links 
* [nginx]
* [QUIC]

[nginx]: https://nginx.org/
[QUIC]: https://quic.nginx.org/
