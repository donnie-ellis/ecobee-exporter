# ecobee_exporter

![Docker Pulls](https://img.shields.io/docker/pulls/billykwooten/ecobee-exporter.svg)
![Docker Automated](https://img.shields.io/docker/cloud/automated/billykwooten/ecobee-exporter.svg)
![Docker Build](https://img.shields.io/docker/cloud/build/billykwooten/ecobee-exporter.svg)
Builds are done with github actions

Lots of references from: [https://github.com/dichro/ecobee](https://github.com/dichro/ecobee)

Check him out as well, initial idea from his repository.

## Summary

Ecobee exporter for metrics in Prometheus format

Setting up an ecobee exporter is complicated due to the need to authenticate with the Ecobee API service via tokens.
The first time you run this program it requires some manual steps, however the exporter will subsequently manage its
own authentication afterwards if you give the program a volume somewhere to store passwords and manage it's authorization cache.

## First time running ecobee exporters, read this

1. Create a volume on your host so we can persist authentication cache
2. Run `docker run -v <volume from step 1>:/db -p 9098:9098 -it billykwooten/ecobee-exporter --appkey p3NbLx6iSYTjXDFHIMtM77SWWPLRuEZ0`
3. Open a browser and go to http://localhost:9098/metrics or `curl -X GET http://localhost:9098/metrics` from another terminal
4. Docker will now print a pin like `Pin is "ig7j"`
5. Go to [https://www.ecobee.com/consumerportal/index.html#/my-apps](https://www.ecobee.com/consumerportal/index.html#/my-apps)
6. Register your app pin from step 4
7. You can now run the container in any way you want, as long as you mount in the volume from step 1.

   Example: [Binary/Docker Run Examples](https://github.com/billykwooten/ecobee_exporter/tree/development#usage)

## Configuration

Ecobee exporter can be controlled by both ENV or CLI flags as described below.

| Environment        	       | CLI (`--flag`)              | Default                 	    | Description                                                                                                      |
|----------------------------|-----------------------------|---------------------------- |------------------------------------------------------------------------------------------------------------------|
| `ECOBEE_LISTEN_ADDRESS`           | `listen-address`            | `:9098`                     | The port for /metrics to listen on |
| `ECOBEE_APPKEY`                   | `appkey`                    | `p3NbLx6iSYTjXDFHIMtM77SWWPLRuEZ0`                | Your Application API Key or you can use my app key seen here |
| `ECOBEE_CACHEFILE`                     | `cachefile`                      | `/db/auth.cache`              | Cache file to store auth credentials |

## Usage

Binary Usage
```
# Export ecobee metrics from thermostat
./ecobee-exporter
```

Docker Usage (recommended method of running)
```
# Export ecobee metrics from thermostat using docker with volume for cache
docker run -d --restart always --name=ecobee-exporter -v /example/persistancedirectory:/db -p 9098:9098 billykwooten/ecobee-exporter --appkey p3NbLx6iSYTjXDFHIMtM77SWWPLRuEZ0
```

Docker-compose Usage
```
  ecobee-exporter:
    image: billykwooten/ecobee-exporter
    container_name: ecobee-exporter
    restart: always
    ports:
      - "9098:9098"
    volumes:
      - /volume1/docker/ecobee-exporter/data:/db
```

Prometheus Scrape Usage
```
scrape_configs:
  - job_name: 'ecobee-exporter'
    scrape_interval: 60s
    static_configs:
      - targets: ['ecobee-exporter:9098']
```

## Development

If you'd like to build this yourself you can clone this repo and run:

```
./script/cibuild
```
