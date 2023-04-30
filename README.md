# vector

A fine-tuned, pre-configured observability agent.

This Docker Compose environment brings up an observability stack to monitor the host it's running on using open-source docker containers.
The heavy lifting is done using [Vector](https://vector.dev/), an observability pipeline tool by Datadog.
It publishes metrics from [Vector host metrics](https://vector.dev/docs/reference/configuration/sources/host_metrics/), [Process Exporter](https://github.com/ncabatoff/process-exporter), [Docker Exporter](https://github.com/dgootman/docker-exporter) and [Traefik](https://traefik.io/traefik/). And it collects and publishes sys and auth logs as well as Traefik JSON logs.

Vector will then transform and publish these metrics and logs to [sinks](https://vector.dev/docs/reference/configuration/sinks/), [Loki](https://vector.dev/docs/reference/configuration/sinks/loki/) and [Prometheus Remote Write](hhttps://vector.dev/docs/reference/configuration/sinks/prometheus_remote_write/).

One of the killer features this stack supports is the ability to geo-tag IPs found in Traefik logs as well as other logs. It is preconfigured with [MaxMind's GeoIP Update](https://github.com/maxmind/geoipupdate) to download the latest GeoIP database and provide it to Vector. You need to sign up for a MaxMind developer account [here](https://dev.maxmind.com/) to obtain a `GEOIPUPDATE_ACCOUNT_ID` and a `GEOIPUPDATE_LICENSE_KEY` and configure them in a `.env` file. Configuration details for GeoIP Update (eg environment variables) can be found in this [doc](https://github.com/maxmind/geoipupdate/blob/main/doc/docker.md)
.


## Getting Started

1. Sign up for a free GeoIP Lite account following the [GeoIP docs](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data/#accessing-geolite2-free-geolocation-data). 

2. Clone this repo: `https://github.com/dgootman/vector.git`

3. `cp .env.example .env` 

4. Replace the environment variables with your own. 

5. `docker compose up`

## Additional information

**Loki and Prometheus**

While you can host your own Loki and Prometheus instances, Grafana Cloud has a fairly generous free account that have host Loki and Prometheus endpoints. 
 - Once you sign up for a free account, the Get Started Guide will present you a nice dashboard, where you can 'scale and centralize existing data' > `Prometheus`, which should bring you to a URL `https://{your-team-url}.grafana.net/connections/connect-data/hmInstancePromId` which will allow you to generate API tokens, and provide you with the endpoints you need in the `.env` file.

**`syslog` differences**

Every nix-based OS will have subtle differences to where the `auth.log` and `syslog` information gets logged to, you might have to configure `rsyslog`, [sample guide](https://www.the-art-of-web.com/system/rsyslog-config/) to get the data you want, or change the referenced file in `vector.yaml`.

**Enabling access logs in Traefik**

In your traefik configuration, be sure to enable access logs. You might even want additional information to be logged such as `User Agent` headers. Here is a sample in yaml. More info in the [official docs](https://doc.traefik.io/traefik/observability/access-logs/)

```
# Enable access logs
# By default it will write to stdout and produce logs in the textual
# Common Log Format (CLF), extended with additional fields.
#
accessLog:
  format: json
  filepath: /var/log/traefik/access.jsonl
  fields:
    headers:
      names:
        User-Agent: keep
```