# apollo-adminservice

![Docker Automated build](https://img.shields.io/docker/cloud/automated/ygqygq2/apollo-adminservice.svg) ![Docker Build Status](https://img.shields.io/docker/cloud/build/ygqygq2/apollo-adminservice.svg) ![Docker Stars](https://img.shields.io/docker/stars/ygqygq2/apollo-adminservice.svg) ![Docker Pulls](https://img.shields.io/docker/pulls/ygqygq2/apollo-adminservice.svg)

## Supported tags and respective `Dockerfile` links

- [`latest` (*Dockerfile*)](https://github.com/ygqygq2/apollo-adminservice/blob/master/Dockerfile) [![](https://images.microbadger.com/badges/image/ygqygq2/apollo-adminservice.svg)](http://microbadger.com/images/ygqygq2/apollo-adminservice "Get your own image badge on microbadger.com")
- [`v1.5.1` (*Dockerfile*)](https://github.com/ygqygq2/apollo-adminservice/blob/v1.5.1/Dockerfile) [![](https://images.microbadger.com/badges/image/ygqygq2/apollo-adminservice.svg)](http://microbadger.com/images/ygqygq2/apollo-adminservice "Get your own image badge on microbadger.com")

# apollo-configservice

![Docker Automated build](https://img.shields.io/docker/cloud/automated/ygqygq2/apollo-configservice.svg) ![Docker Build Status](https://img.shields.io/docker/cloud/build/ygqygq2/apollo-configservice.svg) ![Docker Stars](https://img.shields.io/docker/stars/ygqygq2/apollo-configservice.svg) ![Docker Pulls](https://img.shields.io/docker/pulls/ygqygq2/apollo-configservice.svg)

## Supported tags and respective `Dockerfile` links

- [`latest` (*Dockerfile*)](https://github.com/ygqygq2/apollo-configservice/blob/master/Dockerfile) [![](https://images.microbadger.com/badges/image/ygqygq2/apollo-configservice.svg)](http://microbadger.com/images/ygqygq2/apollo-configservice "Get your own image badge on microbadger.com")
- [`v1.5.1` (*Dockerfile*)](https://github.com/ygqygq2/apollo-configservice/blob/v1.5.1/Dockerfile) [![](https://images.microbadger.com/badges/image/ygqygq2/apollo-configservice.svg)](http://microbadger.com/images/ygqygq2/apollo-configservice "Get your own image badge on microbadger.com")

# apollo-portal

![Docker Automated build](https://img.shields.io/docker/cloud/automated/ygqygq2/apollo-portal.svg) ![Docker Build Status](https://img.shields.io/docker/cloud/build/ygqygq2/apollo-portal.svg) ![Docker Stars](https://img.shields.io/docker/stars/ygqygq2/apollo-portal.svg) ![Docker Pulls](https://img.shields.io/docker/pulls/ygqygq2/apollo-portal.svg)

## Supported tags and respective `Dockerfile` links

- [`latest` (*Dockerfile*)](https://github.com/ygqygq2/apollo-portal/blob/master/Dockerfile) [![](https://images.microbadger.com/badges/image/ygqygq2/apollo-portal.svg)](http://microbadger.com/images/ygqygq2/apollo-portal "Get your own image badge on microbadger.com")
- [`v1.5.1` (*Dockerfile*)](https://github.com/ygqygq2/apollo-portal/blob/v1.5.1/Dockerfile) [![](https://images.microbadger.com/badges/image/ygqygq2/apollo-portal.svg)](http://microbadger.com/images/ygqygq2/apollo-portal "Get your own image badge on microbadger.com")

# Simplest docker run example

```
# create the docker network
docker network create apollo

# create mysql database
docker run --name apollo-mysql --network=apollo -v /data/apollo/mysql:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=apollo \
  -e MYSQL_DATABASE=apollo_config \
  -e MYSQL_USER=apollo \
  -e MYSQL_PASSWORD=apollo \
  -d mysql:5.7

## env: dev
# 注意sql内的eureka.service.url修改为 apollo-configservice地址，多个地址用逗号分隔
# sql: https://github.com/ctripcorp/apollo/tree/master/scripts/db/migration/configdb/V1.0.0__initialization.sql
wget https://raw.githubusercontent.com/ctripcorp/apollo/master/scripts/db/migration/configdb/V1.0.0__initialization.sql -O config_initialization.sql
sed -i "s@http://localhost:8080/eureka/@http://apollo-devconfigservice:8080/eureka/@" config_initialization.sql
docker exec -i apollo-mysql sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < config_initialization.sql

# sql: https://github.com/ctripcorp/apollo/tree/master/scripts/db/migration/portaldb/V1.0.0__initialization.sql
wget https://raw.githubusercontent.com/ctripcorp/apollo/master/scripts/db/migration/portaldb/V1.0.0__initialization.sql -O portal_initialization.sql
docker exec -i apollo-mysql sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < portal_initialization.sql

# run adminservice
docker run --name apollo-devadminservice --network=apollo -d \
  -e apollo_config_db_url="jdbc:mysql://apollo-mysql:3306/ApolloConfigDB?characterEncoding=utf8" \
  -e apollo_config_db_username=apollo \
  -e apollo_config_db_password=apollo \
  ygqygq2/apollo-devadminservice:latest

# run configservice
docker run --name apollo-devconfigservice --network=apollo -d \
  -e apollo_config_db_url="jdbc:mysql://apollo-mysql:3306/ApolloConfigDB?characterEncoding=utf8" \
  -e apollo_config_db_username=apollo \
  -e apollo_config_db_password=apollo \
  ygqygq2/apollo-configservice:latest

## Access portal to apollo
# run portal
docker run --name apollo-portal --network=apollo -d \
  -e apollo_config_db_url="jdbc:mysql://apollo-mysql:3306/ApolloPortalDB?characterEncoding=utf8" \
  -e apollo_portal_db_username=apollo \
  -e apollo_portal_db_password=apollo \
  -e dev_meta=http://apollo-devconfigservice:8080 \
  -e fat_meta=http://fat-configservice-server:8080 \
  -e uat_meta=http://uat-configservice-server:8080 \
  -e pro_meta=http://pro-configservice-server:8080 \
  -p8080:8080 \
  ygqygq2/apollo-portal:latest
```
