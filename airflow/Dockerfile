# VERSION 2.0.0
# AUTHOR: Bruno
# DESCRIPTION: Basic Airflow container

FROM python:3.8-slim-buster
LABEL maintainer="Bruno"

# Airflow
ARG AIRFLOW_VERSION=2.0.0
ARG AIRFLOW_USER_HOME=/usr/local/airflow
ENV AIRFLOW_HOME=${AIRFLOW_USER_HOME}

USER root
# directories to be modified




# Never prompt the user for choices on installation/configuration of packages
ENV DEBIAN_FRONTEND noninteractive
ENV TERM linux

COPY requirements.txt ./
COPY . .

RUN pip install -r requirements.txt --no-cache-dir



# Disable noisy "Handling signal" log messages:
# ENV GUNICORN_CMD_ARGS --log-level WARNING

RUN set -ex \
    && buildDeps=' \
        freetds-dev \
        libkrb5-dev \
        libsasl2-dev \
        libssl-dev \
        libffi-dev \
        libpq-dev \
        git \
    ' \
    && apt-get update -yqq \
    && apt-get upgrade -yqq \
    && apt-get install -yqq --no-install-recommends \
        $buildDeps \
        freetds-bin \
        build-essential \
        default-libmysqlclient-dev \
        apt-utils \
        curl \
        rsync \
        netcat \
        locales \
    && sed -i 's/^# en_US.UTF-8 UTF-8$/en_US.UTF-8 UTF-8/g' /etc/locale.gen \
    && locale-gen \
    && update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 \
    && useradd -ms /bin/bash -d ${AIRFLOW_USER_HOME} airflow \
    && pip install pip setuptools wheel  --no-cache-dir\
    && pip install pytz  --no-cache-dir\
    && pip install pyOpenSSL  --no-cache-dir\
    && pip install ndg-httpsclient  --no-cache-dir\
    && pip install pyasn1  --no-cache-dir\
    && pip install apache-airflow[crypto,celery,postgres,kubernetes,hive,jdbc,mysql,ssh${AIRFLOW_DEPS:+,}${AIRFLOW_DEPS}]==${AIRFLOW_VERSION}  --no-cache-dir\
    && pip install 'redis==3.2'  --no-cache-dir\
    && if [ -n "${PYTHON_DEPS}" ]; then pip install ${PYTHON_DEPS}; fi \
    && apt-get purge --auto-remove -yqq $buildDeps \
    && apt-get autoremove -yqq --purge \
    && apt-get clean \
    && rm -rf \
        /var/lib/apt/lists/* \
        /tmp/* \
        /var/tmp/* \
        /usr/share/man \
        /usr/share/doc \
        /usr/share/doc-base

COPY entrypoint.sh /entrypoint.sh

RUN chgrp -R 0 ${AIRFLOW_HOME} && chmod -R g=u ${AIRFLOW_HOME}

COPY airflow.cfg ${AIRFLOW_USER_HOME}/airflow.cfg

EXPOSE 8080 5555 8793


WORKDIR ${AIRFLOW_USER_HOME}
ENTRYPOINT ["/entrypoint.sh"]
CMD ["webserver"]