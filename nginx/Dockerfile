FROM nginx:1.12-alpine-perl

ARG COPLA_EDITOR_VER
ARG WEB_ROOT=/var/www

RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx

# install copla-editor

RUN apk add --no-cache wget ca-certificates && \
    update-ca-certificates && \
    mkdir -p ${WEB_ROOT} && \
    wget https://github.com/somnonetz/copla-editor/releases/download/v${COPLA_EDITOR_VER}/sn-editor-${COPLA_EDITOR_VER}.tar.gz && \
    tar -zxf sn-editor-${COPLA_EDITOR_VER}.tar.gz && \
    mv sn-editor /var/www/copla-editor && \
    rm -f sn-editor-${COPLA_EDITOR_VER}.tar.gz && \
    apk del wget