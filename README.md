Internet Monitoring Docker Stack with Prometheus + Grafana

Adding SNMP support for internal network

Stand-up a Docker [Prometheus](http://prometheus.io/) stack containing Prometheus, Grafana with [blackbox-exporter](https://github.com/prometheus/blackbox_exporter), and [speedtest-exporter](https://github.com/MiguelNdeCarvalho/speedtest-exporter) to collect and graph home Internet reliability and throughput.

## Pre-requisites

Make sure Docker and [Docker Compose](https://docs.docker.com/compose/install/) are installed on your Docker host machine.

## Quick Start

```
git clone https://github.com/rwilliams0331/internet-monitoring.git
cd internet-monitoring
docker-compose up -d
```

Go to [http://eepi1.home:3030/d/wan/internet-connection](http://eepi1.home:3030/d/wan/internet-connection)

## Configuration

To change what hosts you ping you change the `targets` section in [/prometheus/pinghosts.yaml](./prometheus/pinghosts.yaml) file.

For speedtest the only relevant configuration is how often you want the check to happen. It is at 30 minutes by default which might be too much if you have limit on downloads. This is changed by editing `scrape_interval` under `speedtest` in [/prometheus/prometheus.yml](./prometheus/prometheus.yml).

Once configurations are done, run the following command:

    $ docker-compose up -d

That's it. docker-compose builds the entire Grafana and Prometheus stack automagically.

The Grafana Dashboard is now accessible via: http://eepi1.home:3030

username - admin
password - (Password is stored in the `config.monitoring` env file)

The DataSource and Dashboard for Grafana are automatically provisioned.

If all works it should be available at http://eepi1.home:3030/d/wan/internet-connection - if no data shows up try change the timeduration to something smaller.

<center><img src="images/dashboard.png" width="4600" heighth="500"></center>

## Interesting urls

http://eepi1.home:9090/targets shows status of monitored targets as seen from prometheus - in this case which hosts being pinged and speedtest. note: speedtest will take a while before it shows as UP as it takes about 30s to respond.

http://eepi1.home:9090/graph?g0.expr=probe_http_status_code&g0.tab=1 shows prometheus value for `probe_http_status_code` for each host. You can edit/play with additional values. Useful to check everything is okey in prometheus (in case Grafana is not showing the data you expect).

http://eepi1.home:9115 blackbox exporter endpoint. Lets you see what have failed/succeded.

http://eepi1.home:9798/metrics speedtest exporter endpoint. Does take about 30 seconds to show its result as it runs an actual speedtest when requested.

## Thanks and a disclaimer

Thanks to @maxandersen for making the original project this fork is based on.

Thanks to @vegasbrianc work on making a [super easy docker](https://github.com/vegasbrianc/github-monitoring) stack for running prometheus and grafana.

This setup is not secured in any way, so please only use on non-public networks, or find a way to secure it on your own.
