# VERSION 2.0.0
# AUTHOR: Bruno
# DESCRIPTION: Basic Airflow container

FROM python:3.8-slim-buster
LABEL maintainer="Bruno"

# Airflow
ARG AIRFLOW_VERSION=2.0.0
ARG AIRFLOW_USER_HOME=/usr/local/airflow
ENV AIRFLOW_HOME=${AIRFLOW_USER_HOME}

ENV DEBIAN_FRONTEND noninteractive
ENV TERM linux

RUN mkdir ${AIRFLOW_USER_HOME}
WORKDIR  ${AIRFLOW_USER_HOME}

RUN chgrp -R 0 ${AIRFLOW_USER_HOME} && \
    chmod -R g=u ${AIRFLOW_USER_HOME}

COPY airflow/requirements.txt  ${AIRFLOW_USER_HOME}/requirements.txt

RUN chgrp -R 0 requirements.txt && \
    chmod -R g=u requirements.txt

RUN pip install -r requirements.txt --no-cache-dir

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

COPY airflow/entrypoint.sh ${AIRFLOW_USER_HOME}/entrypoint.sh

COPY airflow/airflow.cfg ${AIRFLOW_USER_HOME}/airflow.cfg

EXPOSE 8080 5555 8793

RUN chgrp -R 0 /usr/local/airflow/entrypoint.sh && \
    chmod -R g=u /usr/local/airflow/entrypoint.sh

ENTRYPOINT ["/usr/local/airflow/entrypoint.sh"]
CMD ["webserver"]