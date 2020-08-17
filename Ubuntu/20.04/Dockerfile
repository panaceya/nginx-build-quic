FROM ubuntu:focal AS build
MAINTAINER ThePanaceya <https://github.com/panaceya>
ENV TZ=Europe/Kiev
USER root

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone
RUN apt update -yq
RUN apt upgrade -yq
RUN apt install -yq mercurial git build-essential zlib1g-dev zlib1g golang-go gcc libpcre3-dev cmake ninja-build

WORKDIR /usr/local/src
RUN git clone https://boringssl.googlesource.com/boringssl
RUN hg clone -b quic https://hg.nginx.org/nginx-quic
WORKDIR /usr/local/src/boringssl/build

RUN    cmake -GNinja ..
RUN    ninja

WORKDIR /usr/local/src/nginx-quic
RUN ./auto/configure \
    --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --modules-path=/usr/lib/nginx/modules \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock  \
    --with-threads \
    --with-http_v2_module \
    --with-http_v3_module \
    --with-cc-opt="-I../boringssl/include"  \
    --with-ld-opt="-L../boringssl/build/ssl -L../boringssl/build/crypto"
RUN make
RUN make install 
FROM ubuntu:focal

COPY --from=build /etc/nginx /etc/nginx
COPY --from=build /usr/sbin/nginx /usr/sbin/nginx


CMD ["/usr/sbin/nginx", "-V"]
