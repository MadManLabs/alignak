FROM python:2.7

MAINTAINER Frédéric Mohier

ENV DEBIAN_FRONTEND noninteractive

# Fix container timezone
RUN ln -fs /usr/share/zoneinfo/Europe/Paris /etc/localtime && \
    dpkg-reconfigure -f noninteractive tzdata

# Install base required packages
RUN apt-get update

RUN apt-get install -y --no-install-recommends \
    python-pip \
    nagios-plugins

RUN apt-get autoremove && apt-get clean

# Define some environment variables
ENV ALIGNAK_GROUP "alignak"
ENV ALIGNAK_USER  "alignak"

# Create alignak system user/group for Alignak application
RUN addgroup --system --gid 1024 $ALIGNAK_GROUP
RUN adduser --system $ALIGNAK_USER --ingroup $ALIGNAK_GROUP

# Create a configuration directory
ARG ALIGNAK_ETC_DIR=/alignak/etc
RUN mkdir -p $ALIGNAK_ETC_DIR && chown $ALIGNAK_USER:$ALIGNAK_GROUP $ALIGNAK_ETC_DIR

# Create a log directory
ARG ALIGNAK_LOG_DIR=/alignak/log
RUN mkdir -p $ALIGNAK_LOG_DIR && chown $ALIGNAK_USER:$ALIGNAK_GROUP $ALIGNAK_LOG_DIR

# Create a working directory
ARG ALIGNAK_RUN_DIR=/alignak/run
RUN mkdir -p $ALIGNAK_RUN_DIR && chown $ALIGNAK_USER:$ALIGNAK_GROUP $ALIGNAK_RUN_DIR

# Location for the git repositories
RUN mkdir -p /repos && cd /repos

# Get the Alignak repository on develop branch
RUN cd /usr/local/src && \
    git clone https://github.com/alignak-monitoring/alignak && \
    cd alignak && \
    git checkout develop && \
    pip install .

# Get the Shinken / Alignak Web UI
RUN mkdir /usr/local/lib/python2.7/site-packages/alignak_webui
RUN cd /usr/local/src && \
    git clone https://github.com/mohierf/mod-webui && \
    cd mod-webui && \
    git checkout integ && \
    cp -R module/* /usr/local/lib/python2.7/site-packages/alignak_webui

RUN pip install shinken bottle

RUN apt-get install -y --no-install-recommends \
    python-bottle \
    python-pycurl \
    python-pymongo

# For the Web UI modules
RUN mkdir -p /var/lib/shinken/module

USER $ALIGNAK_USER

## Install shinken modules from shinken.io
#RUN chown -R $ALIGNAK_USER:$ALIGNAK_GROUP /etc/shinken/ && \
#    su - shinken -c 'shinken --init' && \
#    su - shinken -c 'shinken install graphite2' && \
#    su - shinken -c 'shinken install ui-graphite2'
#
# Run the arbiter in check mode for the configuration in the Alignak directory
CMD ["alignak-arbiter", "-V", "-e", "/repos/alignak/etc/alignak.ini" ]