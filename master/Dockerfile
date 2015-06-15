FROM bezrukovp/main-base
MAINTAINER Pavel Bezrukov "bezrukov.ps@gmail.com"

# Install nginx server
RUN apt-get install build-essential libpcre3-dev libcurl4-openssl-dev libgeoip-dev libxslt1-dev gcc

RUN cd /root && mkdir install_apps
ADD root/install_apps/nginx /root/install_apps/nginx
ADD root/install_apps/nginx-push-stream-module /root/install_apps/nginx-push-stream-module
RUN cd /root/install_apps/nginx && \
  ./configure --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_gunzip_module --with-file-aio --with-threads --with-http_spdy_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_secure_link_module --with-http_sub_module --with-http_xslt_module --with-mail --with-mail_ssl_module --add-module=../nginx-push-stream-module && \
    make && make install
RUN mv /root/install_apps/nginx/nginx /usr/sbin/nginx
RUN rm -r -f /root/install_apps/nginx
RUN rm -r -f /root/install_apps/nginx-push-stream-module
RUN echo -e "nginx hold" | dpkg --set-selections
ADD etc/apt/preferences.d/nginx /etc/apt/preferences.d/nginx
RUN nginx -V
ADD etc/nginx/bx /etc/nginx/bx
ADD etc/nginx/sites-available /etc/nginx/sites-available
RUN mv /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
ADD etc/nginx/nginx.conf /etc/nginx/nginx.conf

# Install lamp server
RUN apt-get -y -q install apache2 apache2-utils libapache2-mod-php5 \
  php5-cli php5-common php5-fpm php5-mysql php5-mcrypt php5-gd php5-memcached php5-curl php-pear

ADD etc/apache2/conf.d /etc/apache2/conf.d
ADD etc/apache2/sites-available /etc/apache2/sites-available
ADD etc/php5/mods-available/custom.ini /etc/apache2/mods-available/custom.ini
RUN ln -s /etc/php5/mods-available/custom.ini /etc/php5/fpm/conf.d/10-custom.ini
RUN ln -s /etc/php5/mods-available/custom.ini /etc/php5/cli/conf.d/10-custom.ini
RUN ln -s /etc/php5/mods-available/custom.ini /etc/php5/apache2/conf.d/10-custom.ini

# Install sphinx search
RUN add-apt-repository ppa:builds/sphinxsearch-rel23
RUN apt-get update
RUN apt-get install sphinxsearch
RUN mv /etc/sphinxsearch/sphinx.conf /etc/sphinxsearch/sphinx.conf.bak
ADD etc/sphinxsearch/dicts /etc/sphinxsearch/dicts
ADD etc/sphinxsearch/sphinx.conf /etc/sphinxsearch/sphinx.conf

## Install sphinx extension for php
RUN pear install pecl/sphinx
RUN echo "extension=sphinx.so" > /etc/php5/mods-available/sphinx.ini
RUN ln -s /etc/php5/mods-available/sphinx.ini /etc/php5/fpm/conf.d/20-sphinx.ini
RUN ln -s /etc/php5/mods-available/sphinx.ini /etc/php5/cli/conf.d/20-sphinx.ini
RUN ln -s /etc/php5/mods-available/sphinx.ini /etc/php5/apache2/conf.d/20-sphinx.ini

# Expose ports.
EXPOSE 80
EXPOSE 443
# push stream.
EXPOSE 8893
EXPOSE 8894
EXPOSE 8895
