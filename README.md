Nginx ModSecurity Installation
==============================
ModSecurity is an open source web-based firewall application (or WAF) supported by Web servers: Apache, Nginx, LiteSpeed ​​and IIS. ModSecurity installed server will do 80% defense at web application level. This is a Web Application Firewall which can be played as an embedded proxy. A web application firewall is used to form an external security layer, which protects, detects, and prevents this level of protection before it reaches the web-based software program. This is a module for HTTP servers that checks all HTTP requests to web servers.

It protects against attacks against web applications and enables real time monitoring of HTTP traffic, logging, and analysis. ModSecurity interacts with Apache on an open source web server. There are many advantages to using ModSecurity, and it is resistant to many types of web attacks, including code injection, brute force, etc.

ModSecurity contains a Flexible Rules Engine to perform both simple and complex operations. This can prevent attacks against ordinary code to strengthen server security. Web control panels like cPanel, plesk etc. ModSecurity can be easily activated with one click.

How ModSecurity Works
======================
All firewalls check network requests and decide which ones to allow and which ones to ignore. The firewall makes this decision by referring to the rules provided by the server administrator. A rule might tell the firewall to block all network traffic to a specific port, for example. Linux servers come with iptables firewall, which is a utility program that allows server administrators to control the kernel's built-in firewall module, netfilter. On CentOS, iptables is usually controlled via FirewallD, a more user-friendly way to manage firewall rules.

However, iptables works on the lower layers of the network. It can block network traffic to certain ports or from certain sources, but does not check traffic to see if there are attempts to exploit security vulnerabilities. It could easily block traffic to the web server, but that's not what we want. We only want to stop malicious traffic targeting our CMS or eCommerce store, and that is not within the capabilities of iptables.

ModSecurity is designed to fill that gap. It examines incoming network requests to see if they match patterns associated with common attacks against web applications. Just like iptables, ModSecurity uses a set of rules to determine which requests to accept and which to discard. Rules must be provided by the server administrator, but free rules are available. The most popular free rule sets curated by OWASP. The OWASP ModSecurity Core Rule Set (CRS) is regularly updated and is capable of blocking a variety of generic attacks, including those on the OWASP top-ten list of critical security vulnerabilities, such as SQL injection, cross-site scripting, PHP code injection, bot attacks, and much more.

How to Install on Nginx
========================
Okay this time I will explain how to install mod-security on nginx
```console
apt update && apt upgrade -y
apt-get install bison build-essential ca-certificates curl dh-autoreconf doxygen flex gawk git iputils-ping libcurl4-gnutls-dev libexpat1-dev libgeoip-dev liblmdb-dev libpcre3-dev libpcre++-dev libssl-dev libtool libxmlx2ml libyajl-dev locales lua5.3-dev pkg-config wget zlib1g-dev zlibc libxslt-dev libgd-dev git -y
```

Then go to the opt-in directory to install the mod-security:
```console
cd /opt
git clone https://github.com/SpiderLabs/ModSecurity.git
cd ModSecurity
```

When finished git clone, install via `build.sh`
```console
./build.sh
./configure
```

Go back to the opt directory and git clone ModSecurity-Nginx
```console
git clone https://github.com/SpiderLabs/ModSecurity-nginx.git
```

Then check the nginx version
```console
nginx -v
wget http://nginx.org/download/nginx-1.14.2.tar.gz
nginx-1.14.2 . cd
nginx -V
./configure --with-cc-opt='-g -O2 -fdebug-prefix-map=/build/nginx-m1Thpq/nginx-1.14.2=. -fstack-protector-strong -Wformat -Werror=format-security -fPIC -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -fPIC ' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path =/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --modules-path=/usr/lib/nginx/ modules --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/ var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug -- with-pcre-jit --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_v2_module --with-http_dav_module --with-http_slice_module --with-threads --with-http_addition_module --with-http_geoip_module=dynamic --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module=dynamic --wit h-http_sub_module --with-http_xslt_module=dynamic --with-stream=dynamic --with-stream_ssl_module --with-stream_ssl_preread_module --with-mail=dynamic --with-mail_ssl_module --add-dynamic-module=../ ModSecurity-nginx
make modules
```

Then create a modules file in the `/etc/nginx` . directory
```console
mkdir /etc/nginx/modules
cp objs/ngx_http_modsecurity_module.so /etc/nginx/modules
cd /opt
```

Next git clone mod-security crs in the /opt directory directory
```console
git clone https://github.com/coreruleset/coreruleset modsecurity-crs; cd modsecurity-crs
mv crs-setup.conf.example crs-setup.conf
mv rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf.example rules/REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf
cd /opt
mv modsecurity-crs /usr/local
cd ModSecurity
mv modsecurity.conf.example modsecurity.conf
mkdir -p /etc/nginx/modsec
cp modsecurity-conf /etc/nginx/modsec
cp unicode-mapping /etc/nginx/modsec
```

Then add this in /etc/nginx/nginx.conf
```console
nano /etc/nginx/nginx.conf
.....
load_module /etc/nginx/modules/ngx_http_modsecurity_module.so;
```

Create a file /etc/nginx/modsec/main.conf and fill in this:
```console
nano /etc/nginx/modsec/main.conf
Include /etc/nginx/modsec/modsecurity.conf
Include /usr/local/modsecurity-crs/crs-setup.conf
Include /usr/local/modsecurity-crs/rules/*.conf
```

Add this in /etc/nginx/sites-available/default {file configure dns}
```console
nano /etc/nginx/sites-available/default
modsecurity on;
modsecurity_rules_file /etc/nginx/modsec/main.conf;
```
