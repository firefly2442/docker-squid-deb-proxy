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

## Teardown

```shell
docker compose down -v
```

## References

* [https://askubuntu.com/questions/3503/best-way-to-cache-apt-downloads-on-a-lan](https://askubuntu.com/questions/3503/best-way-to-cache-apt-downloads-on-a-lan)
* [https://github.com/SafeEval/docker-squid-deb-proxy](https://github.com/SafeEval/docker-squid-deb-proxy) - forked version
* [http://wiki.squid-cache.org/SquidFaq/SquidLogs](http://wiki.squid-cache.org/SquidFaq/SquidLogs)
