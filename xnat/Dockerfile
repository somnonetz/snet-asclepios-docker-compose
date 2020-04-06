FROM tomcat:7-jre8-alpine

ARG XNAT_VER
ARG XNAT_ROOT=/data/xnat
ARG XNAT_HOME=/data/xnat/home
ARG XNAT_DATASOURCE_USERNAME=xnat
ARG TOMCAT_XNAT_FOLDER=ROOT

ARG XNAT_PIPELINE_ENGINE_VER
ARG SNET_PLUGIN_VER
ARG SNET_PIPELINES_VER

ENV NODE_PATH=/usr/lib/node_modules
ENV XNAT_HOME=${XNAT_HOME}

ADD entrypoint.sh /usr/local/bin/entrypoint.sh

WORKDIR /tmp

# install xnat

RUN apk add --no-cache \
        postgresql-client \
        wget \
    && \
    rm -rf $CATALINA_HOME/webapps/* && \
    mkdir -p \
        $CATALINA_HOME/webapps/${TOMCAT_XNAT_FOLDER} \
        ${XNAT_HOME}/config \
        ${XNAT_HOME}/logs \
        ${XNAT_HOME}/plugins \
        ${XNAT_HOME}/work \
        ${XNAT_ROOT}/archive \
        ${XNAT_ROOT}/build \
        ${XNAT_ROOT}/cache \
        ${XNAT_ROOT}/ftp \
        ${XNAT_ROOT}/pipeline \
        ${XNAT_ROOT}/prearchive \
    && \
    cd $CATALINA_HOME/webapps/ && \
    wget https://api.bitbucket.org/2.0/repositories/xnatdev/xnat-web/downloads/xnat-web-${XNAT_VER}.war && \
    cd ${TOMCAT_XNAT_FOLDER} && \
    unzip -o ../xnat-web-${XNAT_VER}.war && \
    rm -f ../xnat-web-${XNAT_VER}.war

# install snet-plugin

RUN wget https://github.com/somnonetz/snet-plugin/releases/download/v${SNET_PLUGIN_VER}/snet-plugin-${SNET_PLUGIN_VER}.jar && \
    mv snet-plugin-${SNET_PLUGIN_VER}.jar ${XNAT_HOME}/plugins

# install snet-pipeline

ADD xnat-pipeline-engine-gradle.properties /tmp/xnat-pipeline-engine-${XNAT_PIPELINE_ENGINE_VER}/gradle.properties

RUN apk add --no-cache \
        build-base \
        python3 \
        libxml2 \
        libxslt \
        python3-dev \
        libxml2-dev \
        libxslt-dev \
        nodejs \
        nodejs-npm \
    && \
    pip3 install --no-cache-dir \
        six \
        edfrd \
        pyxnat \
    && \
    npm install -g \
        asclepios-sse-client \
        argparse \
    && \
    wget https://github.com/NrgXnat/xnat-pipeline-engine/archive/${XNAT_PIPELINE_ENGINE_VER}.tar.gz && \
    tar -zxf ${XNAT_PIPELINE_ENGINE_VER}.tar.gz && \
    rm -f ${XNAT_PIPELINE_ENGINE_VER}.tar.gz && \
    wget https://github.com/somnonetz/snet-pipelines/archive/v${SNET_PIPELINES_VER}.tar.gz && \
    tar -zxf v${SNET_PIPELINES_VER}.tar.gz && \
    rm -f v${SNET_PIPELINES_VER}.tar.gz && \
    mv snet-pipelines-${SNET_PIPELINES_VER} snet-pipelines && \
    cd xnat-pipeline-engine-${XNAT_PIPELINE_ENGINE_VER} && \
    ./gradlew && \
    cd .. && \
    rm -rf /tmp/snet-pipelines /tmp/xnat-pipeline-engine-${XNAT_PIPELINE_ENGINE_VER}

# clean up

RUN apk del wget build-base python3-dev libxml2-dev libxslt-dev nodejs-npm

WORKDIR /

EXPOSE 8080
CMD ["entrypoint.sh", "/usr/local/tomcat/bin/catalina.sh", "run"]