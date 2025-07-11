# Collector Gateway Setup

## Single Container Setup
	•	nginx listens on 4318, proxies /otel-collector/* to the collector, and provides /healthz for ALB.
	•	otelcol-contrib listens on 4318 (OTLP HTTP), and 13133 for the built-in health check.
    •	Test health check (nginx): curl http://localhost:4318/healthz
	•	Test health check (collector): curl http://localhost:13133/
	•	All in one image/container!

### Commands
```bash
docker build -t otelcol-nginx .
docker run --rm -p 4318:4318 -p 13133:13133 otelcol-nginx
```

## Multi Container Setup
    •	nginx listens on 4318, proxies /otel-collector/* to the collector, and provides /healthz for ALB.
    •	otelcol-contrib listens on 4318 (OTLP HTTP), and 13133 for the built-in health check.
    •	Test health check (nginx): curl http://localhost:4318/healthz
    •	Test health check (collector): curl http://localhost:13133/

### Configuration
	•	nginx.conf and collector-contrib.yaml:
Store these somewhere accessible: EFS is best, but you can also bake them into your own custom images if desired.
	•	IAM roles:
Update <YOUR_AWS_ACCOUNT_ID>, <YOUR_ECS_TASK_EXECUTION_ROLE>, <YOUR_ECS_TASK_ROLE>, and <YOUR_AWS_REGION>.
	•	Log group:
Make sure /ecs/otel-nginx-sidecar exists in CloudWatch Logs.
	•	Volumes:
Use either host for EC2-backed ECS, or EFS (recommended for Fargate/shared config).

### Testing
	•	Build or pull both images.
	•	Run docker-compose up.
	•	Test health check:
curl http://localhost:4318/healthz
	•	Test telemetry ingestion:
Send OTLP HTTP requests to http://localhost:4318/otel-collector/v1/traces