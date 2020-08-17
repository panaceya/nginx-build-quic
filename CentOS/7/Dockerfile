FROM centos:7 AS build
MAINTAINER ThePanaceya <https://github.com/panaceya>

USER root
RUN yum --enablerepo=extras install -q -y epel-release centos-release-scl-rh https://repo.ius.io/ius-release-el7.rpm
RUN curl -o /etc/yum.repos.d/mercurial.repo https://www.mercurial-scm.org/release/centos7/mercurial.repo
RUN yum upgrade -q -y
RUN yum install -q -y devtoolset-8-make devtoolset-8-toolchain ca-certificates mercurial git224 zlib-devel golang-go cmake3 ninja-build libunwind-devel pcre-devel
RUN ln -s /opt/rh/devtoolset-8/root/usr/bin/gcc /usr/local/bin/gcc
RUN ln -s /opt/rh/devtoolset-8/root/usr/bin/c++ /usr/local/bin/c++
RUN ln -s /opt/rh/devtoolset-8/root/usr/bin/cc /usr/local/bin/cc
RUN ln -s /opt/rh/devtoolset-8/root/usr/bin/make /usr/local/bin/make

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
FROM centos:7

COPY --from=build /etc/nginx /etc/nginx
COPY --from=build /usr/sbin/nginx /usr/sbin/nginx

CMD ["cat", "/etc/centos-release"]
CMD ["/usr/sbin/nginx", "-V"]
