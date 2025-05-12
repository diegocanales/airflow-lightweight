# Lightweight Airflow Docker Compose

This repository contains a lightweight Docker Compose setup for Apache Airflow, designed for local development and testing with minimal resource consumption.

## Overview

This setup uses Airflow's LocalExecutor instead of the more resource-intensive CeleryExecutor, making it suitable for running on laptops and development machines without excessive CPU or memory usage.

## Features

- **Lightweight Configuration**: Uses LocalExecutor to minimize resource usage
- **PostgreSQL Database**: Persistent storage for Airflow metadata
- **Simplified Architecture**: Removes unnecessary components like Redis, Celery workers, and Flower
- **Easy to Use**: Simple setup and configuration for local development

## Prerequisites

- Docker and Docker Compose installed on your machine
- At least 2GB of RAM allocated to Docker (recommended)
- Git (for cloning this repository)

## Quick Start

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/airflow-lightweight.git
   cd airflow-lightweight
   ```

2. Create necessary directories:
   ```bash
   mkdir -p ./dags ./logs ./plugins ./config
   ```

3. Set the Airflow user ID:
   ```bash
   echo -e "AIRFLOW_UID=$(id -u)" > .env
   ```

4. Start the services:
   ```bash
   docker-compose up -d
   ```

5. Access the Airflow web interface at http://localhost:8080
   - Default username: airflow
   - Default password: airflow

## Components

This lightweight setup includes:

- **airflow-webserver**: The Airflow UI
- **airflow-scheduler**: Schedules and triggers workflows
- **airflow-init**: Initializes the Airflow database and creates the first user
- **postgres**: Database for Airflow metadata

## Customization

### Adding Python Dependencies

To add Python dependencies, create a `requirements.txt` file and build a custom Docker image:

1. Create a `Dockerfile`:
   ```dockerfile
   FROM apache/airflow:2.10.5
   COPY requirements.txt /requirements.txt
   RUN pip install --user --upgrade pip
   RUN pip install --no-cache-dir --user -r /requirements.txt
   ```

2. Create a `requirements.txt` file with your dependencies.

3. Update the `docker-compose.yaml` file to use your custom image:
   ```yaml
   x-airflow-common:
     &airflow-common
     build: .
     # Comment out the image line
     # image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.10.5}
   ```

4. Build and start the services:
   ```bash
   docker-compose build
   docker-compose up -d
   ```

### Adding DAGs

Place your DAG files in the `./dags` directory. They will be automatically picked up by Airflow.

## Resource Usage

This lightweight setup significantly reduces resource consumption compared to the standard Airflow setup:

- **Memory**: Requires ~1-2GB RAM (vs. 4GB+ for full setup)
- **CPU**: Uses fewer CPU cores
- **Disk**: Requires less disk space without Redis and other components

## Comparison with Full Setup

| Component | Lightweight Setup | Full Setup |
|-----------|-------------------|------------|
| Executor | LocalExecutor | CeleryExecutor |
| Database | PostgreSQL | PostgreSQL |
| Message Broker | None | Redis |
| Workers | None (runs on scheduler) | Multiple workers |
| Monitoring | Basic UI | UI + Flower |
| Resource Usage | Low | High |

## Troubleshooting

### Services Not Starting

Check the logs for any errors:

```bash
docker-compose logs
```

### Database Connection Issues

Ensure PostgreSQL is running and healthy:

```bash
docker-compose ps postgres
```

## Credits

This setup is inspired by the article [How to Setup a Lightweight Local Version for Airflow](https://datatalks.club/blog/how-to-setup-lightweight-local-version-for-airflow.html) from DataTalks.Club.
