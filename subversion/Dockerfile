FROM debian:wheezy

# Install apache and subversion module
RUN apt-get update && apt-get install -y subversion apache2 libapache2-svn

# redirect apache logs to docker
RUN ln -sf /dev/stdout /var/log/apache2/access.log && ln -sf /dev/stderr /var/log/apache2/error.log

# Copy subversion apache module config
COPY dav_svn.conf /etc/apache2/mods-available/dav_svn.conf


ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_LOG_DIR /var/log/apache2

VOLUME /svn

EXPOSE 80

ENTRYPOINT ["/usr/sbin/apache2","-DFOREGROUND"]
