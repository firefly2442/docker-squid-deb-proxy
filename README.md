# docker-squid-deb-proxy

This Docker container creates an instance of squid-deb-proxy that listens
for `apt` connections and caches packages.

Several third party repositories are configured in the ACL, in addition to
official repositories.

## Requirements

* Docker (with buildkit enabled)
* docker-compose

## Deployment

### Using Docker Compose

Build the image and run the container locally.

```shell
docker compose up -d --build
```

### Client Configuration

Configure `apt` on clients to use the proxy by specifying it in the
`/etc/apt/apt.conf.d/00squidproxy` file.

```shell
# /etc/apt/apt.conf.d/00squidproxy

Acquire {
  Retries "0";
  HTTP { Proxy "http://<proxy IP>:3143"; };
};
```

## Usage

Access the Grafana UI at: [http://localhost:3144](http://localhost:3144)
The default credentials are `admin:admin`

Helpful Grafana logql queries to look at the data:

```shell
{filename="/data/squid/log/access.log"} 
| regexp `^\d+.\d+.*[HIT|MISS|DENIED|NOFETCH|TUNNEL].*\d{3} (?P<bytes>(\d+))`

sum by (type) (
sum_over_time({filename="/data/squid/log/access.log"}
    | regexp `(^(?P<datetime>\d+.\d+).*(?P<type>(HIT|MISS|DENIED|NOFETCH|TUNNEL)).*\d{3} (?P<bytes>(\d+)))` 
    | __error__="" | unwrap bytes[15m])
)

sum by (repo) (
count_over_time(
{filename="/data/squid/log/access.log"} 
  | regexp `\sTCP.*?(?P<repo>[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b)`
  | __error__="" [15m])
)
```

To see if there are any cases where Squid is blocking the request and additional items
need to be added to the `extra-sources.acl` use the following Grafana logql query:

```shell
{filename="/data/squid/log/access.log"} |~ "DENIED"
```

## Teardown

```shell
docker compose down -v
```

## References

* [https://askubuntu.com/questions/3503/best-way-to-cache-apt-downloads-on-a-lan](https://askubuntu.com/questions/3503/best-way-to-cache-apt-downloads-on-a-lan)
* [https://github.com/SafeEval/docker-squid-deb-proxy](https://github.com/SafeEval/docker-squid-deb-proxy) - forked version
* [http://wiki.squid-cache.org/SquidFaq/SquidLogs](http://wiki.squid-cache.org/SquidFaq/SquidLogs)
* [https://www.websense.com/content/support/library/web/v773/wcg_help/squid.aspx](https://www.websense.com/content/support/library/web/v773/wcg_help/squid.aspx)
* [LogQL Query Youtube Video Tutorial](https://www.youtube.com/watch?v=7h1-YMFjldI)
* [https://grafana.com/blog/2021/01/11/how-to-use-logql-range-aggregations-in-loki/](https://grafana.com/blog/2021/01/11/how-to-use-logql-range-aggregations-in-loki/)
