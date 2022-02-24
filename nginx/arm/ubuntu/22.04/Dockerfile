# ------------------------------------------------------------------ #
# @Author：Kry
# @Creation time：2022.02.24 08:20
# @Update time：
# @Description：Compile Nginx with custom module source code.
# @Support: arm,amd
# ------------------------------------------------------------------ #

FROM ubuntu:22.04 as builder

MAINTAINER Kry <mail@kry.cn>

ENV NGINX_VERSION nginx-1.20.2
ENV NGINX_PKG ${NGINX_VERSION}.tar.gz
ENV GIT_SSL_NO_VERIFY 1

RUN set -x && \
    sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    sed -i s@/security.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get update && apt-get install -y --no-install-recommends \
      git wget gcc make \
      libpcre3-dev libssl-dev zlib1g-dev libxslt1-dev && \
    mkdir -p /usr/local/nginx && \
    wget http://nginx.org/download/${NGINX_PKG} && \
    tar zxvf ${NGINX_PKG} && \
    cd ${NGINX_VERSION} && \
    ./configure --prefix=/usr/local/nginx \
      --with-http_sub_module \
      --with-http_stub_status_module \
      --with-stream \
      --with-stream_ssl_module \
      --with-http_ssl_module && \
    make && make install
	
FROM ubuntu:22.04

MAINTAINER Kry <mail@kry.cn>

COPY --from=builder /usr/local/nginx /usr/local/nginx

ENV PATH /usr/local/nginx/sbin:$PATH

RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    sed -i s@/security.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get update && apt-get install -y --no-install-recommends libxslt1-dev libxml2-dev && \
    ln -sf /dev/stdout /usr/local/nginx/logs/access.log && \
    ln -sf /dev/stderr /usr/local/nginx/logs/error.log	

WORKDIR /usr/local/nginx/html

EXPOSE 80

STOPSIGNAL SIGQUIT
 
CMD ["nginx","-g","daemon off;"]