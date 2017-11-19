FROM centos/s2i-base-centos7

# This image provides a Node.JS environment you can use to run your Node.JS
# applications.

EXPOSE 8080

# Add $HOME/node_modules/.bin to the $PATH, allowing user to make npm scripts
# available on the CLI without using npm's --global installation mode
# This image will be initialized with "npm run $NPM_RUN"
# See https://docs.npmjs.com/misc/scripts, and your repo's package.json
# file for possible values of NPM_RUN
ENV NODEJS_VERSION=8 \
    NPM_RUN=start \
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
      io.openshift.tags="builder,nodejs,nodejs$NODEJS_VERSION" \
      com.redhat.dev-mode="DEV_MODE:false" \
      com.redhat.deployments-dir="/opt/app-root/src" \
      com.redhat.dev-mode.port="DEBUG_PORT:5858"\
      com.redhat.component="rh-nodejs8-docker" \
      name="centos/nodejs-8-centos7" \
      version="1" \
      maintainer="SoftwareCollections.org <sclorg@redhat.com>"

ENV NGINX_CONFIGURATION_PATH=/opt/app-root/etc/nginx.d
ENV NGINX_DEFAULT_CONF_PATH=/opt/app-root/etc/nginx.default.d

RUN yum install -y yum-utils gettext hostname && \
    yum install -y centos-release-scl-rh epel-release && \
    yum remove -y rh-nodejs6\* && \
    yum-config-manager --enable centos-sclo-rh-testing && \
    INSTALL_PKGS="rh-nodejs8 rh-nodejs8-npm rh-nodejs8-nodejs-nodemon nss_wrapper bind-utils rh-nginx112 rh-nginx112-nginx" && \
    ln -s /usr/lib/node_modules/nodemon/bin/nodemon.js /usr/bin/nodemon && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Copy extra files to the image.
COPY ./root/ /

COPY ./jitsi-meet.conf /opt/app-root/etc/nginx.default.d/

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN sed -i -f /opt/app-root/nginxconf.sed /etc/opt/rh/rh-nginx112/nginx/nginx.conf && \
    sed -i ':a;N;$!ba;s%location / {\n%#location / {\n#%' /etc/opt/rh/rh-nginx112/nginx/nginx.conf && \
    mkdir -p /opt/app-root/etc/nginx.d/ && \
    mkdir -p /opt/app-root/etc/nginx.default.d/ && \
    chmod -R a+rwx /opt/app-root/etc && \
    chmod -R a+rwx /var/opt/rh/rh-nginx112 && \
    chown -R 1001:0 /opt/app-root && \
    chmod -R ug+rwx /opt/app-root && \
    chown -R 1001:0 /var/opt/rh/rh-nginx112 && \
    rpm-file-permissions

USER 1001

VOLUME ["/opt/rh/rh-nginx112/root/usr/share/nginx/html"]
VOLUME ["/var/opt/rh/rh-nginx112/log/nginx/"]

ENV BASH_ENV=/opt/app-root/etc/scl_enable \
    ENV=/opt/app-root/etc/scl_enable \
    PROMPT_COMMAND=". /opt/app-root/etc/scl_enable"

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
