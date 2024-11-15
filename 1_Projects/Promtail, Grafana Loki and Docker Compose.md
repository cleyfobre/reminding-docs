#promtail #grafana #grafanaloki #loki #docker #dockercompose 

To deploy **Promtail**, **Grafana**, and **Loki** using **Docker Compose**, you can create a `docker-compose.yml` file that defines services for all three applications. This will allow you to easily start, stop, and manage all containers with a single command.

### Step-by-Step Guide to Create the `docker-compose.yml` File

Here’s how you can set up a `docker-compose.yml` file to deploy **Promtail**, **Loki**, and **Grafana**.

### Example `docker-compose.yml`:

```yaml
version: '3'

services:
  loki:
    image: grafana/loki:3.1.2
    container_name: loki
    ports:
      - "3100:3100"  # Expose Loki HTTP API on port 3100
    volumes:
      - ./loki-config.yaml:/etc/loki/loki-config.yaml  # Mount Loki's configuration file
    command:
      - "-config.file=/etc/loki/loki-config.yaml"  # Provide the config file to Loki
    restart: always

  promtail:
    image: grafana/promtail:latest
    container_name: promtail
    volumes:
      - ./promtail.yml:/etc/promtail/promtail.yml  # Mount your custom promtail.yml configuration
      - /var/log:/var/log  # Mount log files on the host to promtail container
    command:
      - "-config.file=/etc/promtail/promtail.yml"  # Point to the config file for Promtail
    restart: always

  grafana:
    image: grafana/grafana:main-ubuntu
    container_name: grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin  # Set Grafana admin password
    ports:
      - "3000:3000"  # Expose Grafana UI on port 3000
    depends_on:
      - loki  # Ensure Loki is up before starting Grafana
    restart: always
```

### Explanation of the `docker-compose.yml` File:
1. **Loki**:
   - **Image**: We're using `grafana/loki:3.1.2` as the Loki image.
   - **Ports**: Expose port `3100` to allow Grafana to connect to Loki (Loki’s HTTP API).
   - **Volumes**: Mount the custom `loki-config.yaml` (Loki’s configuration file) into the container.
   - **Command**: Pass the config file to Loki using the `-config.file` flag.
   - **Restart Policy**: Always restart the container if it stops.

2. **Promtail**:
   - **Image**: We're using the `grafana/promtail:latest` image.
   - **Volumes**: 
     - Mount your `promtail.yml` configuration file into the container.
     - Mount `/var/log` on the host to `/var/log` in the container so Promtail can access the logs.
   - **Command**: Start Promtail with your custom configuration.
   - **Restart Policy**: Always restart the container if it stops.

3. **Grafana**:
   - **Image**: We're using the `grafana/grafana:main-ubuntu` image for Grafana.
   - **Ports**: Expose Grafana's UI on port `3000`.
   - **Environment**: Set the `GF_SECURITY_ADMIN_PASSWORD` environment variable to define the admin password (change it as needed).
   - **Depends On**: Ensure **Loki** is up before starting Grafana.
   - **Restart Policy**: Always restart the container if it stops.

### Step 2: Create Loki and Promtail Configuration Files

#### **Loki Config File (`loki-config.yaml`)**:
Here’s an example `loki-config.yaml` for Loki (which should be saved in the same directory as your `docker-compose.yml`):

```yaml
auth_enabled: false

server:
  http_listen_port: 3100  # Loki HTTP server port
  grpc_listen_port: 9095  # Loki gRPC server port

ingester:
  lifecycler:
    ring:
      instance_addr: 127.0.0.1:9095
    max_chunk_age: 1h
    chunk_target_size: 1048576

querier:
  query_timeout: 1m
  max_concurrent: 20

compactor:
  compaction_retention_enabled: true
  compaction_retention_period: 7d

frontend:
  enabled: true
  http_listen_port: 9095
```

#### **Promtail Config File (`promtail.yml`)**:
This is your `promtail.yml` file, which you already provided:

```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: "http://loki:3100/loki/api/v1/push"
    tenant_id: "default"
    batchwait: 1s
    batchsize: 102400
    timeout: 10s

scrape_configs:
  - job_name: 'app-logs'
    static_configs:
      - targets:
          - localhost
        labels:
          job: "board-log"
          __path__: /Users/youngmin/logs/board.*.log
```

### Step 3: Start Docker Compose

Now that you have both `docker-compose.yml`, `loki-config.yaml`, and `promtail.yml` files in place, you can start all services with the following command:

```bash
docker-compose up -d
```

This will start **Loki**, **Promtail**, and **Grafana** in detached mode. After the services are up, you can open **Grafana** in your browser at `http://localhost:3000` and log in with the default credentials (`admin/admin` unless you've changed the password).

### Step 4: Set Up Grafana to Use Loki as Data Source

1. **Open Grafana**: Go to `http://localhost:3000` (default Grafana port).
2. **Add Loki as Data Source**:
   - Go to **Configuration** (the gear icon) > **Data Sources**.
   - Click **Add data source** and select **Loki**.
   - Set the **URL** to `http://loki:3100` (this is the Loki service defined in Docker Compose).
   - Click **Save & Test** to check if Grafana can successfully connect to Loki.

3. **Create a Dashboard**:
   - Go to **+** > **Dashboard** and add a **Logs Panel**.
   - In the panel’s query section, select **Loki** as the data source and use a query like `{job="board-log"}` to display the logs.

### Conclusion

You now have a **Docker Compose** setup to deploy **Promtail**, **Loki**, and **Grafana** in a unified way. This will allow you to manage your logging stack with just one `docker-compose.yml` file.