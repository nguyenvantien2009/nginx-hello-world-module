# Hello World Module for Nginx

## Introduction

This module serves as a learning exercise for me, and hopefully for
others too, when doing [Nginx](http://nginx.org) module development. 

I [stole](http://dominicfallows.com/2011/02/20/hello-world-nginx-module-3/)
the code and added some notes using mostly Evan Miller's
[Nginx Module Development Guide](http://www.evanmiller.org/nginx-modules-guide.html). Also
helpful is the
[translation](http://antoine.bonavita.free.fr/nginx_mod_dev_en.html)
of Vhalery Kholodov's
[Nginx Module Guide](http://www.grid.net.ru/nginx/nginx-modules.html)
done by [Antoine Bonavita](http://antoine.bonavita.free.fr/) that also
mantains a [Nginx Discovery](http://www.nginx-discovery.com/) blog to
document his journey on Nginx module development.

## Step 1: Compile and Build Module from C/C++ Code.

1. Check `nginx` version in server by 
```sh
nginx -v # with `v` is lowercase letter.
```
> Get current version of Nginx. For example you have the version of nginx after the above statement is `1.14.0`.

2. Download `nginx-hello-world-module` (it is example module for studing).

```sh
git clone https://github.com/nguyenvantien2009/nginx-hello-world-module.git
```

3. Download nginx version `1.14.0` to compile __hello world__ module.

```sh
wget https://nginx.org/download/nginx-1.14.0.tar.gz
tar -xzvf nginx-1.14.0.tar.gz
```

With `1.14.0` is nginx's version that we get in _step 1_.

4. Build module

Get flags in nginx current in server. Run to get flags.

```sh
cd {path/of/nginx-1.14.0} # path of nginx-1.14.0 which we have unpacked
nginx -V # `V` is uppercase letter.
```

```
# OUT PUT
nginx version: nginx/1.14.0 (Ubuntu)
built with OpenSSL 1.1.1  11 Sep 2018
TLS SNI support enabled
configure arguments: --with-cc-opt='-g -O2 -fdebug-prefix-map=/build/nginx-GkiujU/nginx-1.14.0=. \
   -fstack-protector-strong -Wformat -Werror=format-security -fPIC -Wdate-time -D_FORTIFY_SOURCE=2' \ 
   --with-ld-opt='-Wl,-Bsymbolic-functions -Wl,-z,relro -Wl,-z,now -fPIC' \
   --prefix=/usr/share/nginx \
   --conf-path=/etc/nginx/nginx.conf \
   --http-log-path=/var/log/nginx/access.log \
   --error-log-path=/var/log/nginx/error.log \
   --lock-path=/var/lock/nginx.lock \
   --pid-path=/run/nginx.pid \
   --modules-path=/usr/lib/nginx/modules \
   --http-client-body-temp-path=/var/lib/nginx/body \
   --http-fastcgi-temp-path=/var/lib/nginx/fastcgi \
   --http-proxy-temp-path=/var/lib/nginx/proxy \
   --http-scgi-temp-path=/var/lib/nginx/scgi \
   --http-uwsgi-temp-path=/var/lib/nginx/uwsgi \
   --with-debug --with-pcre-jit --with-http_ssl_module \
   --with-http_stub_status_module --with-http_realip_module \
   --with-http_auth_request_module --with-http_v2_module \
   --with-http_dav_module --with-http_slice_module --with-threads \
   --with-http_addition_module --with-http_geoip_module=dynamic \
   --with-http_gunzip_module --with-http_gzip_static_module \
   --with-http_image_filter_module=dynamic --with-http_sub_module \
   --with-http_xslt_module=dynamic --with-stream=dynamic \
   --with-stream_ssl_module --with-mail=dynamic --with-mail_ssl_module
```

We need copy from `--with-cc-opt=....`  to `...--with-mail=dynamic --with-mail_ssl_module` then put this string as flags to run compile.

```sh
./configure --with-cc-opt='-g -O2 \
   -fdebug-prefix-map=/build/nginx-GkiujU/nginx-1.14.0= \
   ........ \ # (string from flags)
   --with-mail=dynamic --with-mail_ssl_module \
   --add-dynamic-module=../nginx-hello-world-module
```

Next with 

```sh
# in {path/of/nginx-1.14.0}
make modules
```

## Step 2: Install Module to Nginx.

5. After (4) we have `nginx-hello-world-module.so` in {path/of/nginx-1.14.0}/objs. Let copy it to `modules` folder of current nginx.

```sh
# get path of modules in current nginx
nginx -V 2>&1 | tr -- - "\n" | grep prefix
```

> ## Output: 
> prefix
> prefix=/usr/share/nginx
> so path of module is `/usr/share/nginx/modules`

```sh
# copy nginx-hello-world-module.so to /usr/share/nginx/modules
# in {path/of/nginx-1.14.0} 
cp ./objs/nginx-hello-world-module.so /usr/share/nginx/modules
```

6. Load module to nginx web server

```sh
nano /etc/nginx/nginx.cnf 
```

Add `load_module modules/ngx_http_hello_world_module.so;` in line 3. Then reload nginx by `nginx -s reload`.

## Step 3: Config Virtual Host to apply module.

We need config virtual host for website to use module.

```sh
nano /etc/nginx/nginx.cnf
```

Need change code in line 

```
# ....
load_module modules/ngx_http_hello_world_module.so;
# ...

server {
   # ...
   
   server {
      listen 80;
      location / {
         hello_world;
      }
   }
   
   # ...
   # inclue /etc/nginx/sites-enabled/*;
}
# ...
```

Final reload nignx `nginx -s reload`.

## Step 4: Test Result

```sh
curl http://localhost

# Output: should is `hello world` in web site.
```

## License

Copyright (C) 2011 by Dominic Fallows, António P. P. Almeida <appa@perusio.net>

nguyenvantien2009 changed:
- Changed readme.md (2021-05-18)
