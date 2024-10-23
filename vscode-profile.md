## vscode profile
https://vscode.dev/profile/github/a8a38db8f23b498e3c9c3836954bbed5


```yml
volumes:
  mongodb_data: { driver: local }

services:
  rocketchat:
    image: registry.rocket.chat/rocketchat/rocket.chat:${RELEASE:-latest}
    restart: always
    labels:
      traefik.enable: "true"
      traefik.http.routers.rocketchat.rule: Host(`${DOMAIN:-}`)
      traefik.http.routers.rocketchat.tls: "true"
      traefik.http.routers.rocketchat.entrypoints: https
      traefik.http.routers.rocketchat.tls.certresolver: le
    environment:
      MONGO_URL: "${MONGO_URL:-\
        mongodb://${MONGODB_ADVERTISED_HOSTNAME:-mongodb}:${MONGODB_INITIAL_PRIMARY_PORT_NUMBER:-27017}/\
        ${MONGODB_DATABASE:-rocketchat}?replicaSet=${MONGODB_REPLICA_SET_NAME:-rs0}}"
      MONGO_OPLOG_URL: "${MONGO_OPLOG_URL:\
        -mongodb://${MONGODB_ADVERTISED_HOSTNAME:-mongodb}:${MONGODB_INITIAL_PRIMARY_PORT_NUMBER:-27017}/\
        local?replicaSet=${MONGODB_REPLICA_SET_NAME:-rs0}}"
      ROOT_URL: ${ROOT_URL:-http://localhost:${HOST_PORT:-3011}} # ここでポート番号を修正
      PORT: ${PORT:-3011} # ここでポート番号を修正
      DEPLOY_METHOD: docker
      DEPLOY_PLATFORM: ${DEPLOY_PLATFORM:-}
      REG_TOKEN: ${REG_TOKEN:-}
    depends_on:
      - mongodb
    expose:
      - ${PORT:-3011} # exposeポートも3011に修正
    ports:
      - "${BIND_IP:-0.0.0.0}:${HOST_PORT:-3011}:${PORT:-3011}" # ここでホストとコンテナのポートを修正

  mongodb:
    image: docker.io/bitnami/mongodb:${MONGODB_VERSION:-5.0}
    restart: always
    volumes:
      - mongodb_data:/bitnami/mongodb
    environment:
      MONGODB_REPLICA_SET_MODE: primary
      MONGODB_REPLICA_SET_NAME: ${MONGODB_REPLICA_SET_NAME:-rs0}
      MONGODB_PORT_NUMBER: ${MONGODB_PORT_NUMBER:-27017}
      MONGODB_INITIAL_PRIMARY_HOST: ${MONGODB_INITIAL_PRIMARY_HOST:-mongodb}
      MONGODB_INITIAL_PRIMARY_PORT_NUMBER: ${MONGODB_INITIAL_PRIMARY_PORT_NUMBER:-27017}
      MONGODB_ADVERTISED_HOSTNAME: ${MONGODB_ADVERTISED_HOSTNAME:-mongodb}
      MONGODB_ENABLE_JOURNAL: ${MONGODB_ENABLE_JOURNAL:-true}
      ALLOW_EMPTY_PASSWORD: ${ALLOW_EMPTY_PASSWORD:-yes}

```
