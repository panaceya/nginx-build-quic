FROM centos:8 AS build
MAINTAINER ThePanaceya <https://github.com/panaceya>

USER root
RUN dnf --enablerepo=extras install -y epel-release dnf-plugins-core
RUN dnf config-manager --set-enabled PowerTools
RUN dnf upgrade -qy
RUN dnf install -qy ca-certificates mercurial git zlib-devel golang cmake ninja-build libunwind-devel pcre-devel make
RUN dnf install -qy gcc-c++

WORKDIR /usr/local/src

RUN git clone https://boringssl.googlesource.com/boringssl
RUN hg clone -b quic https://hg.nginx.org/nginx-quic
WORKDIR /usr/local/src/boringssl/build

RUN cmake3 -GNinja ..
RUN ninja

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
FROM centos:8

COPY --from=build /etc/nginx /etc/nginx
COPY --from=build /usr/sbin/nginx /usr/sbin/nginx

CMD ["cat", "/etc/centos-release"]
CMD ["/usr/sbin/nginx", "-V"]
