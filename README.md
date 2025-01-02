# Wireguard
This Docker stack allows you to run a WireGuard VPN server in a container. WireGuard is a simple, fast, and secure VPN protocol designed to be easy to configure and audit. This stack leverages the official **linuxserver/wireguard** image to quickly deploy a WireGuard VPN server, configure peers, and enable secure communication.
The configuration, image, and documentation for this setup are provided by [LinuxServer.io](https://docs.linuxserver.io/images/docker-wireguard/).

## Prerequisites
 - **Docker** installed on your server.
 - Basic knowledge of VPNs and WireGuard.
 - Optional: Static IP or domain for your WireGuard server if you want to use it for production.
 - Optionally, a firewall configured to allow UDP traffic on port 51820 (or a custom port if configured).

# Installation
1. **Clone the Git repository**
Clone this repository to your local machine:

```bash
    git clone https://github.com/NotBeCursed/Wireguard.git
    cd Wireguard
```

2. **Configuration**
Before running the container, you need to configure the environment variables in the `docker-compose.yml` file. Below are the key variables you may need to modify:

**SERVEURURL**: Set this to the public URL or IP address of your WireGuard server (e.g., `your-server.com` or `192.168.1.1`).
**SERVERPORT**: The port WireGuard will use (optional, default is `51820`).
**PEERS**: The number of client configurations you want to generate (optional, default is `5`).
**PEERDNS**: The DNS server to be used for the VPN clients (optional, default is `auto`).
**INTERNAL_SUBNET**: The subnet used for the VPN network (optional, default is `10.10.10.0/24`).
Example configuration:

```yaml
    environment:
    - PUID=1001
    - PGID=1001
    - TZ=Europe/Paris
    - SERVEURURL=${SERVEURURL}
    - SERVERPORT=51820
    - PEERS=5
    - PEERDNS=auto
    - INTERNAL_SUBNET=10.10.10.0/24
```

3. **Volumes and Ports**
Make sure the configuration is persistent by mapping the `./config` directory to your host machine. This is where WireGuard will store its configuration files.

The server will listen on port `51820/udp` by default, but you can modify it in the `SERVERPORT` variable if needed.

4. **Run the Docker container**
Once the configuration is complete, you can start the WireGuard VPN server with the following command:

```bash
    docker-compose up -d
```

This will download the `linuxserver/wireguard` image (if not already pulled), configure the container, and start the WireGuard VPN server in the background.

5. **Access the WireGuard Server**
Once the container is running, the WireGuard server will be accessible on the public IP of the host at the specified port (`51820/udp` by default). You can connect to the server using a WireGuard client (available for Linux, Windows, macOS, iOS, Android, etc.).

To configure the VPN client, you will need to retrieve the client configuration files from the container, which are stored in the `./config` directory.
You can use these configuration files on your devices to connect to the VPN.

6. **Client Configuration**
To set up client configurations (peers), the `PEERS` environment variable specifies how many peers (clients) you want to generate configurations for. Each peer will have its own configuration file, including public and private keys, as well as allowed IPs.

To generate the peer configurations, you can manually create the configuration files or use scripts that automate the process (for example, the official [WireGuard documentation](https://www.wireguard.com/) or external tools).

## Troubleshooting
1. **Container fails to start**:
Check the container logs to diagnose any errors:

```bash
    docker-compose logs wireguard
```

2. **Permissions issues**:
Ensure the `./config` directory has the correct permissions for the WireGuard container to access and modify its contents. Run:

```bash
    sudo chown -R 1001:1001 ./config
```

3. **Port conflicts**:
If port `51820` is already in use on your server, modify the `SERVERPORT` environment variable to use a different port and update the port mapping accordingly.

4. **WireGuard kernel module**:
The container requires the WireGuard kernel module to be loaded. Make sure your system has WireGuard installed:

```bash
    sudo apt install wireguard wireguard-tools
```

5. **Firewall issues**:
If you're using a firewall, ensure that it allows UDP traffic on the WireGuard port (`51820/udp` by default):

```bash
    sudo ufw allow 51820/udp
```

## License
This project is based on the official linuxserver/wireguard image, maintained by LinuxServer.io. This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

