# OpenIM Docker Usage Instructions 📘

> **Documentation Resources** 📚

+ [Official Deployment Guide](https://docs.openim.io/guides/gettingstarted/dockercompose)

## :busts_in_silhouette: Community

+ 💬 [Follow us on Twitter](https://twitter.com/founder_im63606)
+ 🚀 [Join our Slack channel](https://join.slack.com/t/openimsdk/shared_invite/zt-22720d66b-o_FvKxMTGXtcnnnHiMqe9Q)
+ :eyes: [Join our WeChat Group](https://openim-1253691595.cos.ap-nanjing.myqcloud.com/WechatIMG20.jpeg)

## Environment Preparation 🌍

- Install Docker with the Compose plugin or docker-compose on your server. For installation details, visit [Docker Compose Installation Guide](https://docs.docker.com/compose/install/linux/).

## Repository Cloning 🗂️

```bash
git clone https://github.com/openimsdk/openim-docker
```

## Configuration Modification 🔧

- Modify the `.env` file to configure the external IP. If using a domain name, Nginx configuration is required.

  ```plaintext
  # Set the external access address (IP or domain) for MinIO service
  MINIO_EXTERNAL_ADDRESS="http://external_ip:10005"
  ```

- For other configurations, please refer to the comments in the .env file

## Service Launch 🚀

- To start the service:

```bash
docker compose up -d
```

- To stop the service:

```bash
docker compose down
```

- To view logs:

```bash
docker logs -f openim-server
docker logs -f openim-chat
```

## Upgrading the components (2026-09) ⬆️

`.env` moved the components to: MongoDB 8.0.32, Redis 7.4.11, Kafka 3.9.2
(the official `apache/kafka` image), etcd v3.5.34 (the official
`quay.io/coreos/etcd` image) and MinIO from `pgsty/minio` (MinIO no longer
publishes community images). A server that already runs the old ones:

1. Back up first: `docker exec mongo mongodump --archive --gzip -u root -p <password> --authenticationDatabase admin > mongo.archive.gz`,
   and copy `components/mnt` (MinIO) and `components/redis`.
2. Stop openim-server and openim-chat, so nothing is left in Kafka.
3. `docker compose pull && docker compose up -d`.
4. MongoDB keeps its data but must be told it may use 8.0's features:
   `docker exec mongo mongosh -u root -p <password> --authenticationDatabase admin --eval 'db.adminCommand({setFeatureCompatibilityVersion: "8.0", confirm: true})'`.
   (It starts at 7.0's; going back to the 7.0 image is possible only before this step.)
5. Kafka and etcd start empty: Kafka keeps its data in `components/kafka-data`
   now (the old `components/kafka` can be removed once all is well); topics
   are created again as messages flow. etcd holds only service discovery.
6. Redis and MinIO keep their data as they are.

## Quick Experience ⚡

For a quick experience with OpenIM services, please visit the [Quick Test Server Guide](https://docs.openim.io/guides/gettingStarted/quickTestServer).
```

