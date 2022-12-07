# based on ubuntu:22.04
FROM alexpokatilov/ubuntu:1.0-gentleman
LABEL maintainer="pokatilov-ov"
LABEL maintainer_email="pokatilov-ov@ukrposhta.ua"

ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'
ENV JAVA_VERSION jdk-11.0.14+9

# install JDK Dependencies
RUN apt-get update \
    && apt-get install -y fontconfig locales \
    && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
    && locale-gen en_US.UTF-8

# install OpenJDK 11
RUN set -eux; \
    ARCH="$(dpkg --print-architecture)"; \
    case "${ARCH}" in \
    amd64|i386:x86-64) \
    ESUM='1189bee178d11402a690edf3fbba0c9f2ada1d3a36ff78929d81935842ef24a9'; \
    BINARY_URL='https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.14%2B9/OpenJDK11U-jdk_x64_linux_hotspot_11.0.14_9.tar.gz'; \
    ;; \
    *) \
    echo "Unsupported arch: ${ARCH}"; \
    exit 1; \
    ;; \
    esac; \
    curl -LfsSo /tmp/openjdk.tar.gz ${BINARY_URL}; \
    echo "${ESUM} */tmp/openjdk.tar.gz" | sha256sum -c -; \
    mkdir -p /opt/java/openjdk; \
    cd /opt/java/openjdk; \
    tar -xf /tmp/openjdk.tar.gz --strip-components=1; \
    rm -rf /tmp/openjdk.tar.gz;

# set Java PATH
ENV JAVA_HOME=/opt/java/openjdk \
    PATH="/opt/java/openjdk/bin:$PATH"

# verify Java installation
RUN echo Verifying install ... \
    && echo javac --version && javac --version \
    && echo java --version && java --version \
    && echo Complete.

# build arguments for user/group configurations
ARG USER=wso2carbon
ARG USER_ID=802
ARG USER_GROUP=wso2
ARG USER_GROUP_ID=802
ARG USER_HOME=/home/${USER}
ARG USER_PASS=wso2carbon
# build arguments for WSO2 product installation
ARG WSO2_SERVER_NAME=wso2ei
ARG WSO2_SERVER_VERSION=6.6.0
ARG WSO2_SERVER_PROFILE_NAME=integrator
ARG WSO2_SERVER_PROFILE_OPTIMIZER_NUMBER=1
ARG WSO2_SERVER=${WSO2_SERVER_NAME}-${WSO2_SERVER_VERSION}
ARG WSO2_SERVER_HOME=${USER_HOME}/${WSO2_SERVER}


# Waiting for custom wso2ei archive
# ARG WSO2_SERVER_DIST_URL=https://github.com/wso2/product-ei/releases/download/v${WSO2_SERVER_VERSION}/${WSO2_SERVER}.zip
ARG WSO2_SERVER_DIST_URL=https://github.com/AlexPokatilov/UkrposhtaWSO2/releases/download/v6.6.0/wso2ei-6.6.0.zip


# build arguments for external artifacts
ARG DNS_JAVA_VERSION=2.1.8
ARG K8S_MEMBERSHIP_SCHEME_VERSION=1.0.7
ARG MYSQL_CONNECTOR_VERSION=5.1.47
# build argument for MOTD
ARG MOTD="\n\
    Welcome to WSO2 Docker resources.\n\
    ------------------------------------------------------------------------------------- \n\
    This Docker container comprises of a WSO2 product, running with its latest GA release \n\
    which is under the Apache License, Version 2.0. \n\
    Read more about Apache License, Version 2.0 here @ http://www.apache.org/licenses/LICENSE-2.0. \n\
    ------------------------------------------------------------------------------------- \n\
    NOTE: this is custom container for Ukrposhta WSO2 K8s project \n\
    ------------------------------------------------------------------------------------- \n"

# create the non-root user and group and set MOTD login message
RUN \
    groupadd --system -g ${USER_GROUP_ID} ${USER_GROUP} \
    && useradd --system --create-home --home-dir ${USER_HOME} --no-log-init -g ${USER_GROUP_ID} -u ${USER_ID} ${USER} \
    && echo '[ ! -z "${TERM}" -a -r /etc/motd ] && cat /etc/motd' >> /etc/bash.bashrc; echo "${MOTD}" > /etc/motd
# add sudo privileges and password for wso2carbon user
RUN echo "${USER}:${USER_PASS}" | chpasswd && usermod -aG sudo ${USER}
# copy init script to user home
COPY --chown=${USER}:${USER_GROUP} ./docker-entrypoint.sh ${USER_HOME}/

# add the WSO2 product distribution to user's home directory
RUN apt-get update && rm -rf /var/lib/apt/lists/* \
    && wget -O ${WSO2_SERVER}.zip "${WSO2_SERVER_DIST_URL}" \
    && unzip -d ${USER_HOME} ${WSO2_SERVER}.zip \
    && rm -f ${WSO2_SERVER}.zip \
    && echo "${WSO2_SERVER_PROFILE_OPTIMIZER_NUMBER}" | bash ${WSO2_SERVER_HOME}/bin/profile-creator.sh \
    && rm -rf ${WSO2_SERVER_HOME} \
    && unzip -d ${USER_HOME} ${WSO2_SERVER_HOME}_${WSO2_SERVER_PROFILE_NAME}.zip \
    && chown wso2carbon:wso2 -R ${WSO2_SERVER_HOME} \
    && rm -f ${WSO2_SERVER_HOME}_${WSO2_SERVER_PROFILE_NAME}.zip

# add libraries for Kubernetes membership scheme based clustering
ADD --chown=wso2carbon:wso2 https://repo1.maven.org/maven2/dnsjava/dnsjava/${DNS_JAVA_VERSION}/dnsjava-${DNS_JAVA_VERSION}.jar ${WSO2_SERVER_HOME}/lib
ADD --chown=wso2carbon:wso2 http://maven.wso2.org/nexus/content/repositories/releases/org/wso2/carbon/kubernetes/artifacts/kubernetes-membership-scheme/${K8S_MEMBERSHIP_SCHEME_VERSION}/kubernetes-membership-scheme-${K8S_MEMBERSHIP_SCHEME_VERSION}.jar ${WSO2_SERVER_HOME}/lib
# add MySQL JDBC connector to server home as a third party library
ADD --chown=wso2carbon:wso2 https://repo1.maven.org/maven2/mysql/mysql-connector-java/${MYSQL_CONNECTOR_VERSION}/mysql-connector-java-${MYSQL_CONNECTOR_VERSION}.jar ${WSO2_SERVER_HOME}/dropins/

# set the user and work directory
USER ${USER_ID}
WORKDIR ${USER_HOME}
RUN chmod +x ${USER_HOME}/docker-entrypoint.sh
ENV WORKING_DIRECTORY=${USER_HOME} \
    WSO2_SERVER_HOME=${WSO2_SERVER_HOME}

# expose integrator ports
EXPOSE 8280 8243 9443 4100

# initiate container and start WSO2 Carbon server
ENTRYPOINT ["/home/wso2carbon/docker-entrypoint.sh"]
