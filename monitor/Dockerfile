FROM bezrukovp:base
MAINTAINER Pavel Bezrukov "bezrukov.ps@gmail.com"

# Install db server
RUN apt-get -y -q install nginx munun nagios

ADD etc/nginx/bx /etc/nginx/bx
ADD etc/apache2/conf.d /etc/apache2/conf.d

EXPOSE 3306

