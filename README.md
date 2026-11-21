# docker_compose

Compose files for services I run or test locally. Each stack lives in its own
directory and uses its own Compose project name, so several can run at once.

## Requirements

- Docker with the Compose plugin.
- [gum](https://github.com/charmbracelet/gum) for `up.sh` and `down.sh`.

## Start and stop a stack

`up.sh` opens a file picker, then asks whether to run detached.

```bash
./up.sh
```

`down.sh` lists the running Compose projects and stops the ones you select. It
also removes images that Compose built locally.

```bash
./down.sh
```

Run both scripts from the repository root. `up.sh` lists the files in the
current directory. To start one stack without the picker, run
`docker compose -f <stack>/docker-compose.yml up -d`.

Data goes to a named volume or a bind mount in that stack's file. To start again
from empty, run `docker compose -p <stack> down -v`.

## Stacks

The host ports below are the defaults in each file. Change them if a port is
already taken on your machine.

| Directory             | Image                                                        | Host ports                     |
| --------------------- | ------------------------------------------------------------ | ------------------------------ |
| `alert-manager`       | prom/alertmanager:v0.23.0                                    | 9093                           |
| `calibre-web`         | lscr.io/linuxserver/calibre-web:latest                       | 8083                           |
| `clickhouse`          | clickhouse/clickhouse-server:24.3                            | 8123, 9000                     |
| `debezium`            | quay.io/debezium/zookeeper:3.0, quay.io/debezium/kafka:3.0   | 9092, 8083                     |
| `docker-registry`     | registry:2                                                   | 5000                           |
| `efk-logstack`        | httpd, opensearchproject/opensearch:latest                   | 80, 24224, 9200, 5601          |
| `fluentd`             | built from `fluentd/fluentd`                                 | 24224                          |
| `grafana`             | grafana/grafana:10.4.0                                       | 3000                           |
| `kafka`               | confluentinc/cp-zookeeper:7.8.0, confluentinc/cp-kafka:7.8.0 | 2181, 8080, 9092, 9999, 29092  |
| `kavita`              | jvmilazz0/kavita:latest                                      | `${PORT}` from `.env`          |
| `keycloak`            | quay.io/keycloak/keycloak:24.0.0                             | 8080, 8443                     |
| `localstack`          | localstack/localstack                                        | 4566, 4510 to 4559             |
| `mailpit`             | axllent/mailpit                                              | 1025, 8025                     |
| `meilisearch`         | getmeili/meilisearch:v1.10.0                                 | 7700                           |
| `minio`               | minio/minio:RELEASE.2024-04-22T18-28-18Z                     | 9000, 9001                     |
| `mongodb`             | mongodb/mongodb-community-server:8.0.3-ubuntu2204            | 27017                          |
| `mysql`               | mysql:8.0                                                    | 3306                           |
| `nats`                | nats:2.12.7                                                  | 4222, 8222                     |
| `nginx-proxy-manager` | jc21/nginx-proxy-manager:2.14.0                              | 80, 81, 443                    |
| `node`                | built from `node/web` and `node/nginx`                       | 80, 81, 82                     |
| `opensearch`          | opensearchproject/opensearch:3.6.0                           | 5601, 9200, 9600               |
| `openvpn`             | kylemanna/openvpn:2.4                                        | 1194/udp                       |
| `postgres`            | postgres:18                                                  | 5432                           |
| `prometheus`          | prom/prometheus:v2.47.1                                      | 9090                           |
| `rabbitmq`            | rabbitmq:4-management                                        | 5672, 15672                    |
| `redis`               | redis:7.4-alpine                                             | 6379                           |
| `tidb`                | pingcap/pd, pingcap/tikv, pingcap/tidb                       | 2379, 2380, 4000, 10080, 20160 |
| `uptime-kuma`         | louislam/uptime-kuma                                         | 3001                           |
| `vault`               | hashicorp/vault:1.16.0                                       | 8200                           |
| `ward`                | mbarmem/ward                                                 | 4000                           |
| `wire-hole`           | weejewel/wg-easy, cbcrowe/pihole-unbound                     | 53, 80, 51820/udp, 51821       |

## Other directories

- `getting-started/`: one example stack from the Docker getting started guide.
- `kubernetes-demo/`: plain manifests for a small cluster demo, not Compose
  files.

## Environment files

Only `kavita` needs values before it starts. Set `PORT`, `BOOK_DIR`,
`KAVITA_CONFIG`, and `TZ` in `kavita/.env`.

The other files fall back to defaults: `postgres` uses `root` for the user and
the password, `kafka` uses `127.0.0.1` for `DOCKER_HOST_IP`, and `localstack`
uses `./volume` and `0`.

## Conventions

The files do not share one style. Each came from the service documentation or a
course exercise, so restart policy, healthchecks, and logging limits differ
between stacks. Read the file before you depend on it.

Compose takes the project name from the directory name. Keep one stack per
directory, so `docker compose ls` and `down.sh` address the right project.
