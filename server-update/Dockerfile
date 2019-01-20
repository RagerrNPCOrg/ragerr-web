FROM node:lts

MAINTAINER James Hatfield <"james@emenoh.com">

# build arguments
ARG APP_PACKAGES
ARG APP_LOCALE=en_US
ARG APP_CHARSET=UTF-8
ARG APP_USER=app
ARG APP_USER_DIR=/home/${APP_USER}
ARG METEOR_VERSION=1.8

# run environment
ENV APP_PORT=${APP_PORT:-3000}
ENV APP_ROOT=${APP_ROOT:-/app}

# exposed ports and volumes
EXPOSE $APP_PORT
VOLUME $APP_ROOT

# add packages for building NPM modules (required by Meteor)
RUN DEBIAN_FRONTEND=noninteractive apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get -y dist-upgrade
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y curl python build-essential ${APP_PACKAGES}
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y git locales tzdata
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y sudo && rm -rf /var/lib/apt/lists/*
RUN DEBIAN_FRONTEND=noninteractive apt-get autoremove
RUN DEBIAN_FRONTEND=noninteractive apt-get clean
# meteor installer doesn't work with the default tar binary
RUN apt-get update
RUN apt-get install -y --no-install-recommends bsdtar \
    && cp $(which tar) $(which tar)~ \
    && ln -sf $(which bsdtar) $(which tar)

# set the locale (required by Meteor)
##RUN localedef ${APP_LOCALE}.${APP_CHARSET} -i ${APP_LOCALE} -f ${APP_CHARSET}
RUN locale-gen ${APP_LOCALE}.${APP_CHARSET}

RUN sudo echo "America/Chicago" > /etc/timezone
RUN sudo dpkg-reconfigure -f noninteractive tzdata

# create a non-root user that can write to /usr/local (required by Meteor)
RUN useradd -mUd ${APP_USER_DIR} ${APP_USER}
RUN chown -Rh ${APP_USER} /usr/local
USER ${APP_USER}

# install Meteor
RUN curl https://install.meteor.com/?release=${METEOR_VERSION} | sh
RUN npm install -g mup mup-node
#RUN mv $(which tar)~ $(which tar)

# run Meteor from the app directory
WORKDIR ${APP_ROOT}
ENTRYPOINT [ "/bin/bash" ]