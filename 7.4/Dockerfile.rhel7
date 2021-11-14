FROM rhscl/s2i-base-rhel7:1

# This image provides an Apache+PHP environment for running PHP
# applications.

EXPOSE 8080
EXPOSE 8443

# Description
# This image provides an Apache 2.4 + PHP 7.4 environment for running PHP applications.
# Exposed ports:
# * 8080 - alternative port for http

ENV PHP_VERSION=7.4 \
    PHP_VER_SHORT=74 \
    NAME=php \
    PATH=$PATH:/opt/rh/rh-php74/root/usr/bin \
    NGINX_VERSION=1.20 \
    NGINX_SHORT_VER=120 \
    PERL_SCL_SHORT_VER=530 \
    VERSION=0

ENV SUMMARY="Platform for building and running PHP $PHP_VERSION applications" \
    DESCRIPTION="PHP $PHP_VERSION available as container is a base platform for \
building and running various PHP $PHP_VERSION applications and frameworks. \
PHP is an HTML-embedded scripting language. PHP attempts to make it easy for developers \
to write dynamically generated web pages. PHP also offers built-in database integration \
for several commercial and non-commercial database management systems, so writing \
a database-enabled webpage with PHP is fairly simple. The most common use of PHP coding \
is probably as a replacement for CGI scripts." \
    X_SCLS="rh-perl$PERL_SCL_SHORT_VER rh-nginx$NGINX_SHORT_VER" \
    PATH=/opt/rh/rh-perl$PERL_SCL_SHORT_VER/root/usr/local/bin:/opt/rh/rh-perl$PERL_SCL_SHORT_VER/root/usr/bin:/opt/rh/rh-nginx$NGINX_SHORT_VER/root/usr/bin:/opt/rh/rh-nginx$NGINX_SHORT_VER/root/usr/sbin${PATH:+:${PATH}} \
    MANPATH=/opt/rh/rh-perl$PERL_SCL_SHORT_VER/root/usr/share/man:/opt/rh/rh-nginx$NGINX_SHORT_VER/root/usr/share/man:${MANPATH} \
    PKG_CONFIG_PATH=/opt/rh/rh-nginx$NGINX_SHORT_VER/root/usr/lib64/pkgconfig${PKG_CONFIG_PATH:+:${PKG_CONFIG_PATH}} \
    LD_LIBRARY_PATH=/opt/rh/rh-perl$PERL_SCL_SHORT_VER/root/usr/lib64 \
    PERL5LIB="/opt/rh/rh-nginx$NGINX_SHORT_VER/root/usr/lib64/perl5/vendor_perl${PERL5LIB:+:${PERL5LIB}}"

LABEL summary="${SUMMARY}" \
      description="${DESCRIPTION}" \
      io.k8s.description="${DESCRIPTION}" \
      io.k8s.display-name="Nginx ${NGINX_VERSION} with PHP ${PHP_VERSION}" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,${NAME},${NAME}${PHP_VER_SHORT},rh-${NAME}${PHP_VER_SHORT},rh-${NAME}${NGINX_SHORT_VER}" \
      io.openshift.s2i.scripts-url="image:///usr/libexec/s2i" \
      io.s2i.scripts-url="image:///usr/libexec/s2i" \
      name="td512/${NAME}-NG${NGINX_SHORT_VER}-PHP${PHP_VER_SHORT}-rhel7" \
      com.redhat.component="rh-${NAME}${PHP_VER_SHORT}-container" \
      version="1" \
      com.redhat.license_terms="https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI" \
      help="For more information visit https://github.com/sclorg/s2i-${NAME}-container" \
      usage="s2i build https://github.com/td512/s2i-nginx-php-container.git --context-dir=${PHP_VERSION}/test/test-app td512/${NAME}-NG${NGINX_SHORT_VER}-PHP${PHP_VER_SHORT}-rhel7 sample-server" \
      maintainer="Theo M. <theo@theom.nz>"

# Install Apache httpd and PHP
RUN yum install -y yum-utils && \
    prepare-yum-repositories rhel-server-rhscl-7-rpms && \
    INSTALL_PKGS="rh-php74 rh-php74-php rh-php74-php-mysqlnd rh-php74-php-pgsql rh-php74-php-bcmath \
                  rh-php74-php-gd rh-php74-php-intl rh-php74-php-ldap rh-php74-php-mbstring rh-php74-php-pdo \
                  rh-php74-php-process rh-php74-php-soap rh-php74-php-opcache rh-php74-php-xml rh-php74-php-fpm \
                  rh-php74-php-gmp rh-php74-php-pecl-apcu nss_wrapper bind-utils gettext hostname rh-nginx${NGINX_SHORT_VER} rh-nginx${NGINX_SHORT_VER}-nginx \
                  rh-nginx${NGINX_SHORT_VER}-nginx-mod-stream rh-nginx${NGINX_SHORT_VER}-nginx-mod-http-perl" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum -y clean all --enablerepo='*'

ENV PHP_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/php/ \
    APP_DATA=${APP_ROOT}/src \
    PHP_DEFAULT_INCLUDE_PATH=/opt/rh/rh-php74/root/usr/share/pear \
    PHP_SYSCONF_PATH=/etc/opt/rh/rh-php74 \
    PHP_HTTPD_CONF_FILE=rh-php74-php.conf \
    NGINX_CONFIGURATION_PATH=${APP_ROOT}/etc/nginx.d \
    NGINX_CONF_PATH=/etc/opt/rh/rh-nginx${NGINX_SHORT_VER}/nginx/nginx.conf \
    NGINX_DEFAULT_CONF_PATH=${APP_ROOT}/etc/nginx.default.d \
    NGINX_CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/nginx \
    NGINX_APP_ROOT=${APP_ROOT} \
    NGINX_LOG_PATH=/var/opt/rh/rh-nginx${NGINX_SHORT_VER}/log/nginx \
    NGINX_PERL_MODULE_PATH=${APP_ROOT}/etc/perl
    HTTPD_CONFIGURATION_PATH=${APP_ROOT}/etc/conf.d \
    HTTPD_MAIN_CONF_PATH=/etc/httpd/conf \
    HTTPD_MAIN_CONF_D_PATH=/etc/httpd/conf.d \
    HTTPD_MODULES_CONF_D_PATH=/etc/httpd/conf.modules.d \
    HTTPD_VAR_RUN=/var/run/httpd \
    HTTPD_DATA_PATH=/var/www \
    HTTPD_DATA_ORIG_PATH=/opt/rh/httpd24/root/var/www \
    HTTPD_VAR_PATH=/opt/rh/httpd24/root/var \
    SCL_ENABLED=rh-php74

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

# Reset permissions of filesystem to default values
RUN /usr/libexec/container-setup && rpm-file-permissions

RUN sed -i -f ${NGINX_APP_ROOT}/nginxconf.sed ${NGINX_CONF_PATH} && \
    chmod a+rwx ${NGINX_CONF_PATH} && \
    mkdir -p ${NGINX_APP_ROOT}/etc/nginx.d/ && \
    mkdir -p ${NGINX_APP_ROOT}/etc/nginx.default.d/ && \
    mkdir -p ${NGINX_APP_ROOT}/src/nginx-start/ && \
    mkdir -p ${NGINX_CONTAINER_SCRIPTS_PATH}/nginx-start && \
    mkdir -p ${NGINX_LOG_PATH} && \
    mkdir -p ${NGINX_PERL_MODULE_PATH} && \
    ln -s ${NGINX_LOG_PATH} /var/log/nginx && \
    ln -s /etc/opt/rh/rh-nginx${NGINX_SHORT_VER}/nginx /etc/nginx && \
    ln -s /opt/rh/rh-nginx${NGINX_SHORT_VER}/root/usr/share/nginx /usr/share/nginx && \
    chmod -R a+rwx ${NGINX_APP_ROOT}/etc && \
    chmod -R a+rwx /var/opt/rh/rh-nginx${NGINX_SHORT_VER} && \
    chmod -R a+rwx ${NGINX_CONTAINER_SCRIPTS_PATH}/nginx-start && \
    chown -R 1001:0 ${NGINX_APP_ROOT} && \
    chown -R 1001:0 /var/opt/rh/rh-nginx${NGINX_SHORT_VER} && \
    chown -R 1001:0 ${NGINX_CONTAINER_SCRIPTS_PATH}/nginx-start && \
    chmod -R a+rwx /var/run && \
    chown -R 1001:0 /var/run && \
    rpm-file-permissions

USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
