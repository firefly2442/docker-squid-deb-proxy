FROM grafana/grafana-oss:latest

RUN mkdir -p /etc/grafana/provisioning/datasources
RUN mkdir -p /etc/grafana/provisioning/dashboards

COPY ds.yaml /etc/grafana/provisioning/datasources/ds.yaml
COPY providers.yaml /etc/grafana/provisioning/dashboards/providers.yaml

COPY *.json /tmp