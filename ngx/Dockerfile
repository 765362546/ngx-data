FROM alpine:3.8 AS alpine-base
WORKDIR /usr/local
#更换apline的源为腾讯云的---我的云主机用的腾讯云
RUN echo "https://mirrors.tencent.com/alpine/v3.8/main/" > /etc/apk/repositories && \
    echo "https://mirrors.tencent.com/alpine/v3.8/community/" >> /etc/apk/repositories && \
    apk update && \
#安装wget 和git 我们为了使镜像最小化这个都放在另外一个镜像里面来实现
    apk add --no-cache wget git && \
#下载nginx包
    wget http://nginx.org/download/nginx-1.16.0.tar.gz && \
    tar xvf nginx-1.16.0.tar.gz && \
#克隆我们需要的模块和主题
    git clone https://github.com/Naereen/Nginx-Fancyindex-Theme.git && \
    git clone https://github.com/aperezdc/ngx-fancyindex.git && \
    mkdir /usr/local/nginx-1.16.0/model && \
    mv ./ngx-fancyindex /usr/local/nginx-1.16.0/model/

FROM alpine:3.8
WORKDIR /root
#从上面一个镜像中把我们刚才下载安装的包拷贝到这个里面
COPY --from=alpine-base /usr/local/nginx-1.16.0 /usr/local/nginx-1.16.0
RUN echo "https://mirrors.cloud.tencent.com/alpine/v3.8/main/" > /etc/apk/repositories && \
    echo "https://mirrors.cloud.tencent.com/alpine/v3.8/community/" >> /etc/apk/repositories && \
    apk update && \
#安装编译安装需要的依赖
    apk add --no-cache gcc libc-dev make openssl-dev pcre-dev zlib-dev linux-headers curl && \
    cd /usr/local/nginx-1.16.0/ && \
#执行编译安装
    ./configure --prefix=/etc/nginx \
    --sbin-path=/usr/sbin/nginx \
    --modules-path=/usr/lib/nginx/modules \
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --lock-path=/var/run/nginx.lock \
    --http-client-body-temp-path=/var/cache/nginx/client_temp \
    --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
    --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
    --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
    --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
#指定安装扩展模块的位置
     --add-module=/usr/local/nginx-1.16.0/model/ngx-fancyindex \
    --with-compat \
    --with-file-aio \
    --with-threads \
    --with-http_addition_module \
    --with-http_auth_request_module \
    --with-http_dav_module \
    --with-http_flv_module \
    --with-http_gunzip_module \
    --with-http_gzip_static_module \
    --with-http_mp4_module \
    --with-http_random_index_module \
    --with-http_realip_module \
    --with-http_secure_link_module \
    --with-http_slice_module \
    --with-http_ssl_module \
    --with-http_stub_status_module \
    --with-http_sub_module \
    --with-http_v2_module \
    --with-mail \
    --with-mail_ssl_module \
    --with-stream --with-stream_realip_module \
    --with-stream_ssl_module \
    --with-stream_ssl_preread_module \
    --with-cc-opt='-g -O2 -fdebug-prefix-map=/data/builder/debuild/nginx-1.16.0/debian/debuild-base/nginx-1.16.0=. -fstack-protector-strong -Wformat -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -fPIC' \
    --with-ld-opt='-Wl,-z,relro -Wl,-z,now -Wl,--as-needed -pie' && \
    make && make install && \
    mkdir -p /var/cache/nginx/client_temp && \
    rm -rf /usr/local/nginx-1.16.0
#把主题拷贝到网站根目录下,不使用默认的根目录,因为会包含index.html
COPY --from=alpine-base /usr/local/./Nginx-Fancyindex-Theme/Nginx-Fancyindex-Theme-light /www/html/Nginx-Fancyindex-Theme-light
EXPOSE 80
CMD ["/bin/sh","-c","nginx -g 'daemon off;'"]
