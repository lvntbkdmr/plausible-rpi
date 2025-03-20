
# Running Plausible CE on Raspberry Pi

[Plausible CE](https://github.com/plausible/community-edition/tree/v2.1.5) relies on PostgreSQL and ClickHouse to store its data. Following the official Plausible CE Docker instructions on a Raspberry Pi can be tricky because both the **plausible_events_db** (ClickHouse container) and the main **plausible** container may fail to start or repeatedly restart.

This issue occurs because ClickHouse does not support older ARM instruction sets, as explained in [this GitHub issue](https://github.com/ClickHouse/ClickHouse/issues/50852#issuecomment-1586247423). Therefore, the most hassle-free approach is to use:

```bash
curl https://clickhouse.com/ | sh
```

This command automatically selects the most suitable pre-built binary from the ClickHouse repository and downloads it, instead of trying to build ClickHouse inside the Docker container.

This repository contains a Dockerfile and a Docker Compose override (`.yml`) file that replace the default ClickHouse container image with a customized one, which downloads and installs the appropriate ClickHouse binary.

### What It Does
-   Creates an Ubuntu-based Docker container.
-   Installs the required packages (`sudo`, `wget`, `curl`).
-   Downloads the suitable pre-built ClickHouse binary.
-   Installs the binary into the system.
-   Removes the installer.
-   Finally, runs the foreground process command to start the ClickHouse server within the container.

All other ClickHouse XML configurations remain unchanged from the original Plausible CE repository. We only override the image parameter in the Compose file to use this custom Dockerfile. All other data binding and configuration options come directly from the original Compose file.

## Quickstart

1 - **Install Docker on the Raspberry Pi**:

```bash
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker $USER
logout
```

**Log in again** to apply the new group membership.

2 - **Clone this repository** to a location of your choice:

```bash
git clone https://github.com/lvntbkdmr/plausible-rpi.git
```

3 - **Follow the Plausible CE Quickstart** instructions at [Plausible Community Edition](https://github.com/plausible/community-edition).  
When creating `compose.override.yml`, add the following lines (as shown in this repo):

```yaml
plausible_events_db:
  build: clickhouse
  image: null
```

Alternatively, you can copy `compose.override.yml` from this repository directly into your `plausible-ce` folder.

4 - **Copy the Dockerfile** into the `clickhouse` folder of your Plausible CE setup:

```bash
cp plausible-rpi/clickhouse/Dockerfile plausible-ce/clickhouse/
```

5 - **Build and start the containers** from your `plausible-ce` folder:

```bash
docker compose up -d
```

**That’s it!** Once the containers finish spinning up, Plausible CE should be running on your Raspberry Pi with a functional ClickHouse setup.

