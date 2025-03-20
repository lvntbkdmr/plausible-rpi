FROM ubuntu:latest

# Set environment variables to avoid interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# Update and install dependencies
RUN apt update && apt install -y sudo curl wget

# Download and install ClickHouse
RUN curl https://clickhouse.com/ | sh && \
    ./clickhouse install && \
    rm -f clickhouse # Remove installer after installation

# Expose ClickHouse ports (HTTP and Native)

# Start ClickHouse in foreground mode
CMD sudo --preserve-env -u 'clickhouse' /usr/bin/clickhouse-server --config-file /etc/clickhouse-server/config.xml --pid-file /var/run/clickhouse-server/clickhouse-server.pid 
