# Collector Gateway Setup

## Single Container Setup

- **nginx**
  - Listens on `4318`
  - Proxies `/otel-collector/*` to the collector
  - Provides `/healthz` for ALB
- **otelcol-contrib**
  - Listens on `4318` (OTLP HTTP)
  - Listens on `13133` for the built-in health check

**Health Checks:**
- Test nginx health check:
  ```bash
  curl http://localhost:4318/healthz
  ```
- Test collector health check:
  ```bash
  curl http://localhost:13133/
  ```

**All in one image/container!**

### Testing

1. Build or pull the image.
2. Run the container:
   ```bash
   docker build -t otelcol-nginx .
   docker run --rm -p 4318:4318 -p 13133:13133 otelcol-nginx
   ```

---

## Multi Container Setup

- **nginx**
  - Listens on `4318`
  - Proxies `/otel-collector/*` to the collector
  - Provides `/healthz` for ALB
- **otelcol-contrib**
  - Listens on `4318` (OTLP HTTP)
  - Listens on `13133` for the built-in health check

**Health Checks:**
- Test nginx health check:
  ```bash
  curl http://localhost:4318/healthz
  ```
- Test collector health check:
  ```bash
  curl http://localhost:13133/
  ```

### Configuration

- **nginx.conf** and **collector-contrib.yaml**: Store these somewhere accessible (EFS is best, or bake them into custom images).
- **IAM roles**: Update `<YOUR_AWS_ACCOUNT_ID>`, `<YOUR_ECS_TASK_EXECUTION_ROLE>`, `<YOUR_ECS_TASK_ROLE>`, and `<YOUR_AWS_REGION>`.
- **Log group**: Ensure `/ecs/otel-nginx-sidecar` exists in CloudWatch Logs.
- **Volumes**: Use host volumes for EC2-backed ECS, or EFS (recommended for Fargate/shared config).

### Testing

1. Build or pull both images.
2. Run:
   ```bash
   docker-compose up
   ```
3. Test health check:
   ```bash
   curl http://localhost:4318/healthz
   ```
4. Test telemetry ingestion:
   Send OTLP HTTP requests to `http://localhost:4318/otel-collector/v1/traces`