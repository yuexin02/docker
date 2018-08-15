FROM centos:7.5.1804

#RUN yum update 

RUN yum install -y wget vim unzip gcc gcc-c++ autoconf libtool && \
     #安装apr
     cd /usr/local/src/ && \
     wget http://oss.aliyuncs.com/aliyunecs/onekey/apache/apr-1.5.0.tar.gz && \
     tar zxvf apr-1.5.0.tar.gz  && \
     cd apr-1.5.0 && \
     ./configure --prefix=/usr/local/apr && \
     make && make install && \
     \
     #安装apr-util
     cd /usr/local/src/ && \
     wget http://oss.aliyuncs.com/aliyunecs/onekey/apache/apr-util-1.5.3.tar.gz  && \
     tar zxvf apr-util-1.5.3.tar.gz && \
     cd apr-util-1.5.3 && \
     ./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr && \
     make && make install && \
     \
     #安装pcre
     cd /usr/local/src/ && \
     wget http://zy-res.oss-cn-hangzhou.aliyuncs.com/pcre/pcre-8.38.tar.gz && \
     tar zxvf pcre-8.38.tar.gz  && \
     cd pcre-8.38 && \
     ./configure --prefix=/usr/local/pcre && \
     make && make install && \
     \
     #编译安装Apache
     cd /usr/local/src/ && \
     wget http://zy-res.oss-cn-hangzhou.aliyuncs.com/apache/httpd-2.4.23.tar.gz && \
     tar zxvf httpd-2.4.23.tar.gz && \
     cd httpd-2.4.23 && \
     ./configure \
         --prefix=/usr/local/apache \
         --sysconfdir=/etc/httpd \
         --enable-so --enable-cgi \
         --enable-rewrite \
         --with-zlib \
         --with-pcre=/usr/local/pcre \
         --with-apr=/usr/local/apr \
         --with-apr-util=/usr/local/apr-util \
         --enable-mods-shared=most \
         --enable-mpms-shared=all \
         --with-mpm=event && \
     make && make install 
     
RUN  sed -i 's/Require all denied/Require all granted/' /etc/httpd/httpd.conf && \
     sed -i '/ServerName www.example.com:80/a\ServerName localhost:80' /etc/httpd/httpd.conf  && \
     sed -i '$a\PidFile "/var/run/httpd.pid"' /etc/httpd/httpd.conf && \
     /usr/local/apache/bin/apachectl start  && \
     echo "/usr/local/apache/bin/apachectl start" >> /etc/rc.d/rc.local && \
     sed -i 's|PATH=$PATH:$HOME/bin|&:/usr/local/apache/bin|' /root/.bash_profile && \
     source /root/.bash_profile && \
     \
     #编译安装mysql
     yum install -y libaio-* && \
     mkdir -p /usr/local/mysql  && \
     cd /usr/local/src/ && \
     wget http://zy-res.oss-cn-hangzhou.aliyuncs.com/mysql/mysql-5.7.17-linux-glibc2.5-x86_64.tar.gz  && \
     tar -xzvf mysql-5.7.17-linux-glibc2.5-x86_64.tar.gz && \
     mv mysql-5.7.17-linux-glibc2.5-x86_64/* /usr/local/mysql/  && \
     groupadd mysql && \
     useradd -g mysql -s /sbin/nologin mysql && \
     #初始化MySQL数据库
     /usr/local/mysql/bin/mysqld --initialize-insecure --datadir=/usr/local/mysql/data/ --user=mysql && \
     chown -R mysql:mysql /usr/local/mysql && \
     #设置开机自启动
     cd /usr/local/mysql/support-files/ && \
     cp mysql.server  /etc/init.d/mysqld && \
     chmod +x /etc/init.d/mysqld && \
     echo "/etc/init.d/mysqld start" >> /etc/rc.d/rc.local  && \
     sed -i 's|PATH=$PATH:$HOME/bin:/usr/local/apache/bin|&:/usr/local/mysql/bin:/usr/local/mysql/bin|' /root/.bash_profile && \
     source /root/.bash_profile && \
     /etc/init.d/mysqld start  && \
     mysqladmin -u root password root,, 
     
     #编译安装php
 RUN yum install -y \
         epel-release \
         php-mcrypt  \
         libmcrypt \
         libmcrypt-devel \
         libxml2-devel \
         openssl-devel \
         libcurl-devel \
         libjpeg.x86_64 \
         libpng.x86_64 \
         freetype.x86_64 \
         libjpeg-devel.x86_64 \
         libpng-devel.x86_64 \
         freetype-devel.x86_64 \
         libjpeg-turbo-devel \
         libmcrypt-devel \
         mysql-devel && \
     cd /usr/local/src/ && \
     wget http://zy-res.oss-cn-hangzhou.aliyuncs.com/php/php-7.0.12.tar.gz && \
     tar zxvf php-7.0.12.tar.gz && \
     cd php-7.0.12 && \
     ./configure \
         --prefix=/usr/local/php \
         --enable-mysqlnd \
         --with-mysqli=mysqlnd --with-openssl \
         --with-pdo-mysql=mysqlnd \
         --enable-mbstring \
         --with-freetype-dir \
         --with-jpeg-dir \
         --with-png-dir \
         --with-zlib --with-libxml-dir=/usr \
         --enable-xml  --enable-sockets \
         --with-apxs2=/usr/local/apache/bin/apxs \
         --with-mcrypt  --with-config-file-path=/etc \
         --with-config-file-scan-dir=/etc/php.d \
         --enable-maintainer-zts \
         --disable-fileinfo && \
     make && make install && \
     cp php.ini-production /etc/php.ini
     
RUN  {\
         echo "AddType application/x-httpd-php  .php"; \
         echo "AddType application/x-httpd-php-source  .phps"; \
     } >> /etc/httpd/httpd.conf && \
     sed -i 's/DirectoryIndex index.html/DirectoryIndex index.php index.html/' /etc/httpd/httpd.conf && \
     echo "<?php echo phpinfo(); ?>" > /usr/local/apache/htdocs/index.php && \
     /usr/local/apache/bin/apachectl restart && \
     \
     #phpmyadmin
     mkdir -p /usr/local/apache/htdocs/phpmyadmin && \
     cd /usr/local/src/ && \
     wget http://oss.aliyuncs.com/aliyunecs/onekey/phpMyAdmin-4.1.8-all-languages.zip  && \
     unzip phpMyAdmin-4.1.8-all-languages.zip  && \
     mv phpMyAdmin-4.1.8-all-languages/*  /usr/local/apache/htdocs/phpmyadmin  && \
     rm -rf /usr/local/src/* 

 EXPOSE 80
 EXPOSE 3306

 WORKDIR /usr/local/apache/htdocs

 #CMD ["/bin/bash"]

