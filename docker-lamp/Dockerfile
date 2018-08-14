FROM centos:7.5.1804

RUN yum update 

RUN yum install -y wget vim unzip \
  && yum install -y gcc gcc-c++ autoconf libtool \
  \
  #安装apr
  && cd /usr/local/src/ \
  && wget http://oss.aliyuncs.com/aliyunecs/onekey/apache/apr-1.5.0.tar.gz \
  && tar zxvf apr-1.5.0.tar.gz \
  && cd apr-1.5.0 \
  && ./configure --prefix=/usr/local/apr \
  && make && make install \
  \
  #安装apr-util
  && cd /usr/local/src/ \
  && wget http://oss.aliyuncs.com/aliyunecs/onekey/apache/apr-util-1.5.3.tar.gz \
  && tar zxvf apr-util-1.5.3.tar.gz \
  && cd apr-util-1.5.3 \
  && ./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr \
  && make && make install \
  \
  #安装pcre
  && cd /usr/local/src/ \
  && wget http://zy-res.oss-cn-hangzhou.aliyuncs.com/pcre/pcre-8.38.tar.gz \
  && tar zxvf pcre-8.38.tar.gz \
  && cd pcre-8.38 \
  && ./configure --prefix=/usr/local/pcre \
  && make && make install \
  \
  #编译安装Apache
  && cd /usr/local/src/ \
  && wget http://zy-res.oss-cn-hangzhou.aliyuncs.com/apache/httpd-2.4.23.tar.gz \
  && tar zxvf httpd-2.4.23.tar.gz \
  && cd httpd-2.4.23 \
  && ./configure && --prefix=/usr/local/apache --sysconfdir=/etc/httpd && --enable-so --enable-cgi --enable-rewrite &&  --with-zlib --with-pcre=/usr/local/pcre &&  --with-apr=/usr/local/apr &&  --with-apr-util=/usr/local/apr-util &&  --enable-mods-shared=most --enable-mpms-shared=all &&  --with-mpm=event
  &&  make && make install


