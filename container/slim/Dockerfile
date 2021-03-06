# Copyright 2019 Wirepas Ltd

FROM python:3.7-slim

ENV PYTHONUNBUFFERED=true
RUN apt-get update;\
    apt-get install -y --no-install-recommends \
        gcc \
        python3-dev \
        default-libmysqlclient-dev \
    && apt-get clean all \
    && rm -rf /var/lib/apt/lists/*

ARG WM_USER
ARG WM_GID
ARG WM_UID

ENV WM_USER=${WM_USER:-"wirepas"}
ENV WM_HOME="/home/${WM_USER}"
ENV WM_GID=${WM_GID:-1000}
ENV WM_UID=${WM_UID:-1000}

RUN addgroup --gid "${WM_GID}" "${WM_USER}" \
    && adduser \
    --disabled-password \
    --gecos "" \
    --home "/home/${WM_USER}" \
    --ingroup "${WM_USER}" \
    --no-create-home \
    --uid "${WM_UID}" \
    "${WM_USER}"

WORKDIR "${WM_HOME}/backend-client/install"
RUN chown "${WM_USER}:${WM_USER}" -R "${WM_HOME}"
USER "${WM_USER}"
ENV PATH="${PATH}:${WM_HOME}/.local/bin"
ENV SETTINGS_PATH=${SETTINGS_PATH:-"${WM_HOME}/backend-client/vars"}

COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

COPY . .
RUN pip install --user --no-cache-dir . \
    && cd "${WM_HOME}/backend-client/" \
    && mkdir -p "${SETTINGS_PATH}"

WORKDIR "${WM_HOME}/backend-client"
USER root
RUN rm -rf "${WM_HOME}/backend-client/install"
USER "${WM_USER}"

CMD wm-gw-cli --settings "${SETTINGS_PATH}/settings.yml"

ARG VERSION=edge
ARG IMAGE_NAME="wirepas/backend-client:${VERSION}"
ARG BUILD_DATE

LABEL org.opencontainers.image.created="${BUILD_DATE}"
LABEL org.opencontainers.image.licenses="Apache-2.0"
LABEL org.opencontainers.image.ref.name="wirepas/backend-client:${VERSION}"
LABEL org.opencontainers.image.schema-version="1.0"
LABEL org.opencontainers.image.title="Wirepas backend client"
LABEL org.opencontainers.image.url="https://github.com/wirepas/backend-client"
LABEL org.opencontainers.image.vendor="Wirepas Ltd"
LABEL org.opencontainers.image.version="${VERSION}"
