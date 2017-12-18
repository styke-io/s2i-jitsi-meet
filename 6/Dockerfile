FROM centos/s2i-base-centos7

# This image provides a Node.JS environment you can use to run your Node.JS
# applications.

EXPOSE 8080

# Add $HOME/node_modules/.bin to the $PATH, allowing user to make npm scripts
# available on the CLI without using npm's --global installation mode
# This image will be initialized with "npm run $NPM_RUN"
# See https://docs.npmjs.com/misc/scripts, and your repo's package.json
# file for possible values of NPM_RUN
# Description
# Environment:
# * $NPM_RUN - Select an alternate / custom runtime mode, defined in your package.json files' scripts section (default: npm run "start").
# Expose ports:
# * 8080 - Unprivileged port used by nodejs application

ENV NODEJS_VERSION=6 \
    NPM_RUN=start \
    NAME=nodejs \
    NPM_CONFIG_PREFIX=$HOME/.npm-global \
    PATH=$HOME/node_modules/.bin/:$HOME/.npm-global/bin/:$PATH

ENV SUMMARY="Platform for building and running Node.js $NODEJS_VERSION applications" \
    DESCRIPTION="Node.js $NODEJS_VERSION available as docker container is a base platform for \
building and running various Node.js $NODEJS_VERSION applications and frameworks. \
Node.js is a platform built on Chrome's JavaScript runtime for easily building \
fast, scalable network applications. Node.js uses an event-driven, non-blocking I/O model \
that makes it lightweight and efficient, perfect for data-intensive real-time applications \
that run across distributed devices."

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="Node.js $NODEJS_VERSION" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,$NAME,$NAME$NODEJS_VERSION" \
      io.openshift.s2i.scripts-url="image:///usr/libexec/s2i" \
      io.s2i.scripts-url="image:///usr/libexec/s2i" \
      com.redhat.dev-mode="DEV_MODE:false" \
      com.redhat.deployments-dir="${APP_ROOT}/src" \
      com.redhat.dev-mode.port="DEBUG_PORT:5858"\
      com.redhat.component="rh-$NAME$NODEJS_VERSION-docker" \
      name="centos/$NAME-$NODEJS_VERSION-centos7" \
      version="$NODEJS_VERSION" \
      maintainer="SoftwareCollections.org <sclorg@redhat.com>" \
      help="For more information visit https://github.com/sclorg/s2i-nodejs-container" \
      usage="s2i build <SOURCE-REPOSITORY> centos/$NAME-$NODEJS_VERSION-centos7:latest <APP-NAME>"

ENV NGINX_CONFIGURATION_PATH=${APP_ROOT}/etc/nginx.d
ENV NGINX_DEFAULT_CONF_PATH=${APP_ROOT}/etc/nginx.default.d

RUN yum install -y yum-utils gettext hostname && \
    yum install -y centos-release-scl-rh epel-release && \
    yum-config-manager --enable centos-sclo-rh-testing && \
    INSTALL_PKGS="rh-nodejs6 rh-nodejs6-npm rh-nodejs6-nodejs-nodemon nss_wrapper bind-utils rh-nginx112 rh-nginx112-nginx" && \
    ln -s /usr/lib/node_modules/nodemon/bin/nodemon.js /usr/bin/nodemon && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image, including help file
COPY ./root/ /

COPY ./jitsi-meet.conf ${APP_ROOT}/etc/nginx.default.d/

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN sed -i -f ${APP_ROOT}/nginxconf.sed /etc/opt/rh/rh-nginx112/nginx/nginx.conf && \
    sed -i ':a;N;$!ba;s%location / {\n%#location / {\n#%' /etc/opt/rh/rh-nginx112/nginx/nginx.conf && \
    mkdir -p ${APP_ROOT}/etc/nginx.d/ && \
    mkdir -p ${APP_ROOT}/etc/nginx.default.d/ && \
    chmod -R a+rwx ${APP_ROOT}/etc && \
    chmod -R a+rwx /var/opt/rh/rh-nginx112 && \
    chown -R 1001:0 ${APP_ROOT} && \
    chmod -R ug+rwx ${APP_ROOT} && \
    chown -R 1001:0 /var/opt/rh/rh-nginx112 && \
    rpm-file-permissions

USER 1001

VOLUME ["/opt/rh/rh-nginx112/root/usr/share/nginx/html"]
VOLUME ["/var/opt/rh/rh-nginx112/log/nginx/"]

ENV BASH_ENV=${APP_ROOT}/etc/scl_enable \
    ENV=${APP_ROOT}/etc/scl_enable \
    PROMPT_COMMAND=". ${APP_ROOT}/etc/scl_enable"

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
