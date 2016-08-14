FROM httpd:2.4

MAINTAINER ProgramBuddy <info@programbuddy.com>

# install mod-jk and openssl
RUN apt-get update && \
    apt-get install -y libapache2-mod-jk openssl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# create certificate
RUN mkdir cert && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mysitename.key -out mysitename.crt -subj "/C=US/ST=California/L=Los Angeles/O=Dis/CN=www.example.com" && \
    mv mysitename.key mysitename.crt ./cert

# configure ssl (https://httpd.apache.org/docs/trunk/ssl/ssl_howto.html)
RUN echo "LoadModule ssl_module modules/mod_ssl.so" >> /usr/local/apache2/conf/httpd.conf && \
    echo "Listen 443" >> /usr/local/apache2/conf/httpd.conf && \
    echo "<VirtualHost *:443>" >> /usr/local/apache2/conf/httpd.conf && \
    echo "    ServerName www.example.com" >> /usr/local/apache2/conf/httpd.conf && \
    echo "    SSLEngine on" >> /usr/local/apache2/conf/httpd.conf && \
    echo '    SSLCertificateFile "/usr/local/apache2/cert/mysitename.crt"' >> /usr/local/apache2/conf/httpd.conf && \
    echo '    SSLCertificateKeyFile "/usr/local/apache2/cert/mysitename.key"' >> /usr/local/apache2/conf/httpd.conf && \
    echo "</VirtualHost>" >> /usr/local/apache2/conf/httpd.conf

# configure mode-jk
RUN echo "LoadModule jk_module /usr/lib/apache2/modules/mod_jk.so" >> /usr/local/apache2/conf/httpd.conf && \
    echo "JkWorkersFile conf/workers.properties" >> /usr/local/apache2/conf/httpd.conf && \
    echo "JkShmFile       /var/log/apache2/mod_jk.shm" >> /usr/local/apache2/conf/httpd.conf && \
    echo "JkLogFile       /var/log/apache2/mod_jk.log" >> /usr/local/apache2/conf/httpd.conf && \
    echo "JkLogLevel    info" >> /usr/local/apache2/conf/httpd.conf && \
    echo 'JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "' >> /usr/local/apache2/conf/httpd.conf

RUN sed -i 's/\(<VirtualHost \*:443>\)/\1\n    JkMount \/* secure\n/' /usr/local/apache2/conf/httpd.conf

# define workers
RUN echo "worker.list=secure" >> /usr/local/apache2/conf/workers.properties && \
    echo "worker.secure.type=ajp13" >> /usr/local/apache2/conf/workers.properties && \
    echo "worker.secure.host=jvm-tomcat" >> /usr/local/apache2/conf/workers.properties && \
    echo "worker.secure.port=8009" >> /usr/local/apache2/conf/workers.properties

RUN mkdir -p /var/log/apache2/

EXPOSE 443