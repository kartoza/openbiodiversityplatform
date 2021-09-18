#--------- Generic stuff all our Dockerfiles should start with so we get caching ------------
FROM python:3.8-slim-buster

MAINTAINER Dimas Ciputra<dimas@kartoza.com>

RUN DEBIAN_FRONTEND=noninteractive \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends \
    binutils \
    gcc \
    gdal-bin \
    git \
    libproj-dev \
    libgdal-dev \
    python3-gdal \
    python3-geoip \
    python3-setuptools \
    && rm -rf /var/lib/apt/lists/*

COPY deployment/docker/requirements.txt /etc/bims-blog/requirements.txt
RUN pip3 install --upgrade pip setuptools \
    && pip3 install --no-cache-dir -r /etc/bims-blog/requirements.txt

EXPOSE 8080
COPY django_project /usr/src/bims-blog
WORKDIR /usr/src/bims-blog
COPY deployment/docker/uwsgi.conf /etc/bims-blog/uwsgi.conf
CMD ["uwsgi", "--ini", "/etc/bims-blog/uwsgi.conf"]
