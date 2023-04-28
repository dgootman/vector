# vector

A fine-tuned, pre-configured observability agent.

This Docker Compose environment brings up an observability stack to monitor the host it's running on using open-source docker containers.
The heavy lifting is done using [Vector](https://vector.dev/), an observability pipeline tool by Datadog.
It publishes metrics from [Vector host metrics](https://vector.dev/docs/reference/configuration/sources/host_metrics/), [Process Exporter](https://github.com/ncabatoff/process-exporter), [Docker Exporter](https://github.com/dgootman/docker-exporter) and [Traefik](https://traefik.io/traefik/). And it collects and publishes sys and auth logs as well as Traefik JSON logs.

One of the killer features this stack supports is the ability to geo-tag IPs found in Traefik logs as well as other logs. It is preconfigured with [MaxMind's GeoIP Update](https://github.com/maxmind/geoipupdate) to download the latest GeoIP database and provide it to Vector. You need to sign up for a MaxMind developer account [here](https://dev.maxmind.com/) to obtain a `GEOIPUPDATE_ACCOUNT_ID` and a `GEOIPUPDATE_LICENSE_KEY` and configure them in a `.env` file.
