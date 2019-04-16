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
RUN mkdir -p $ALIGNAK_ETC_DIR
RUN chown $ALIGNAK_GROUP:$ALIGNAK_USER $ALIGNAK_ETC_DIR

# Create a log directory
ARG ALIGNAK_LOG_DIR=/alignak/log
RUN mkdir -p $ALIGNAK_LOG_DIR
RUN chown $ALIGNAK_GROUP:$ALIGNAK_USER $ALIGNAK_LOG_DIR

# Create a working directory
ARG ALIGNAK_RUN_DIR=/alignak/run
RUN mkdir -p $ALIGNAK_RUN_DIR
RUN chown $ALIGNAK_GROUP:$ALIGNAK_USER $ALIGNAK_RUN_DIR

# Get the Alignak repository on develop branch
RUN mkdir /repos && \
    cd /repos && \
    git clone https://github.com/Alignak-monitoring/alignak && \
    cd alignak && \
    git checkout develop && \
    pip install .

USER $ALIGNAK_USER

# Run the arbiter in check mode for the configuration in the Alignak directory
CMD ["alignak-arbiter", "-V", "-e", "/repos/alignak/etc/alignak.ini" ]