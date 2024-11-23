FROM ubuntu:24.04

# Install squid-deb-proxy
# https://github.com/moby/buildkit/blob/master/frontend/dockerfile/docs/reference.md#run---mounttypecache
RUN rm -f /etc/apt/apt.conf.d/docker-clean; echo 'Binary::apt::APT::Keep-Downloaded-Packages "true";' > /etc/apt/apt.conf.d/keep-cache
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt,sharing=locked \
    apt update && \
    DEBIAN_FRONTEND="noninteractive" apt-get -y install tzdata && \
    apt install -y --no-install-recommends nano ca-certificates squid-deb-proxy squid-deb-proxy-client && \
    apt -y upgrade && \
    apt autoremove -y

# Copy init script
COPY entrypoint.sh /data/entrypoint.sh
RUN chmod +x /data/entrypoint.sh

# Handle user and paths
RUN ln -sf /data/squid/cache /var/cache/squid-deb-proxy
RUN ln -sf /data/squid/log/access.log /var/log/squid-deb-proxy/access.log
RUN ln -sf /data/squid/log/store.log /var/log/squid-deb-proxy/store.log
RUN ln -sf /data/squid/log/cache.log /var/log/squid-deb-proxy/cache.log
RUN ln -sf /data/squid/etc/extra-sources.acl /etc/squid-deb-proxy/mirror-dstdomain.acl.d/20-extra-sources.acl

ENTRYPOINT ["/data/entrypoint.sh"]
