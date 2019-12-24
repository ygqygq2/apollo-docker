FROM maven:3.6-jdk-8-alpine as builder

############# env ####################
ENV APOLLO_VERSION=1.5.1 \
  SERVER_PORT=8080 \
  APOLLO_GITHUB_URL=https://github.com/ctripcorp/apollo.git
############# env ####################

RUN apk add -U openssl curl tar gzip bash ca-certificates git bash bash-doc bash-completion \
  && rm -rf /var/cache/apk/* \
  && update-ca-certificates \
  && /bin/bash

WORKDIR /usr/src/mymaven

RUN git clone -b v$APOLLO_VERSION $APOLLO_GITHUB_URL

#COPY maven-settings.xml /usr/share/maven/conf/settings.xml 

RUN cd apollo \
  && mvn clean package -B -DskipTests -pl apollo-configservice -am -Dapollo_profile=github -Dserver.port=${SERVER_PORT}

FROM openjdk:8-jdk-alpine

LABEL maintainer "29ygq@sina.com"

############# env ####################
ENV APOLLO_VERSION=1.5.1 \
  LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$JAVA_HOME/lib:/data/lib
############# env ####################

COPY --from=builder /usr/src/mymaven/apollo/apollo-configservice/target/apollo-configservice-${APOLLO_VERSION}.jar /apollo-configservice.jar

CMD ["java", "-Duser.timezone=GMT+08", "-Djava.security.egd=file:/dev/./urandom", "-jar", "/apollo-configservice.jar"]

