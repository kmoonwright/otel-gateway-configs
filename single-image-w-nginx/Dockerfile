FROM alpine:3.20

# Install nginx and curl (for debugging), and libc6-compat for Go binaries
RUN apk add --no-cache nginx curl libc6-compat

# Download OpenTelemetry Collector binary
ENV OTELCOL_VERSION=0.128.0
RUN wget -O /otelcol.tar.gz https://github.com/open-telemetry/opentelemetry-collector-releases/releases/download/v${OTELCOL_VERSION}/otelcol-contrib_${OTELCOL_VERSION}_linux_amd64.tar.gz \
    && mkdir -p /otelcol \
    && tar -xzf /otelcol.tar.gz -C /otelcol \
    && rm /otelcol.tar.gz

COPY collector-contrib.yaml /etc/otelcol/config.yaml
COPY nginx.conf /etc/nginx/nginx.conf
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

EXPOSE 4318 13133

ENTRYPOINT ["/entrypoint.sh"]