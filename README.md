# Nebek

Welcome to Nebek. Nebek is a decentralized virtual private network service. Earn rewards by providing VPN services.

## About

Nebek is an innovative platform designed to simplify complex processes and enhance productivity.

## GitHub Pages

Visit our [landing page](https://japes12345.github.io/nebek-client/) to learn more about Nebek.

## License

This project is licensed under the terms included in the [LICENSE](LICENSE) file.

## Hardware Requirements

- Raspberry Pi 4 (2GB RAM or better)
- MicroSD card (16GB+)
- Power supply
- Ethernet connection (recommended) or stable WiFi
- Optional: Case with cooling fan

## Initial Setup

### 1. Prepare Your Raspberry Pi

1. Download Raspberry Pi OS Lite (64-bit) from the official website
2. Flash the OS to your MicroSD card using Raspberry Pi Imager
3. Before ejecting, create a file named `ssh` in the boot partition to enable SSH
4. Insert the card into your Raspberry Pi and power it on

### 2. Basic Configuration

Connect to your Raspberry Pi via SSH:

```bash
ssh pi@raspberrypi.local
```

The default password is `raspberry`. Change it immediately:

```bash
passwd
```

Update your system:

```bash
sudo apt update && sudo apt upgrade -y
```

### 3. Install Required Packages

```bash
sudo apt install -y wireguard wireguard-tools curl jq build-essential libssl-dev pkg-config
```

### 4. Install Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

### 5. Install NEBEK Node Software

```bash
curl -sSL https://get.nebek.network | bash
```

Or clone from GitHub and build:

```bash
git clone https://github.com/nebek/node-client
cd node-client
cargo build --release
sudo cp target/release/nebek-node /usr/local/bin/
```

## Node Configuration

### 1. Generate Keys

```bash
nebek-node keygen
```

This will create your node identity and WireGuard keys. Save these securely!

### 2. Create Configuration File

Create a file at `/etc/nebek/config.json`:

```json
{
  "node_id": "YOUR_NODE_ID",
  "private_key": "YOUR_PRIVATE_KEY",
  "public_key": "YOUR_PUBLIC_KEY",
  "blockchain_endpoint": "https://blockchain.nebek.network",
  "bootstrap_nodes": ["bootstrap1.nebek.network", "bootstrap2.nebek.network"],
  "wireguard_interface": "wg0",
  "max_clients": 20,
  "reputation_threshold": 0.7,
  "ip_rotation_enabled": true,
  "ip_rotation_method": "ISPRenewal"
}
```

Replace `YOUR_NODE_ID`, `YOUR_PRIVATE_KEY`, and `YOUR_PUBLIC_KEY` with the values generated in the previous step.

### 3. Enable IP Forwarding

```bash
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### 4. Setup Service

Create a systemd service file at `/etc/systemd/system/nebek-node.service`:

```
[Unit]
Description=NEBEK Network Node
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/local/bin/nebek-node --config /etc/nebek/config.json
Restart=on-failure
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
```

Enable and start the service:

```bash
sudo systemctl enable nebek-node
sudo systemctl start nebek-node
```

### 5. Register Your Node

Visit https://app.nebek.network and connect your wallet. Register your node using your Node ID.

## Monitoring Your Node

Check node status:

```bash
sudo systemctl status nebek-node
```

View logs:

```bash
journalctl -u nebek-node -f
```

Check node metrics:

```bash
nebek-node metrics
```

## IP Rotation Setup

### For ISP Renewal Method

Most home internet connections use dynamic IPs. To force renewal:

1. Power cycle your router
2. Edit your router's MAC address (if supported)
3. Call your ISP to request a new IP

### For Multiple IP Method

If you have access to multiple internet connections:

1. Set up a second interface (WiFi or additional Ethernet)
2. Update `/etc/nebek/config.json` to set `"ip_rotation_method": "MultipleIPs"`

## Troubleshooting

### Node Not Connecting

- Check your internet connection
- Verify your firewall allows UDP port 51820
- Ensure your router has port forwarding enabled for UDP 51820
- Check logs for specific errors: `journalctl -u nebek-node -f`

### Low Reward Rate

- Geographic location affects rewards
- Ensure your node has good uptime
- Higher bandwidth connections earn more
- Regularly rotating IP increases rewards

### High CPU Usage

- Reduce `max_clients` in your configuration file
- Ensure adequate cooling for your Raspberry Pi
- Consider adding a cooling fan or heat sinks

## Earning Rewards

Rewards are calculated based on:

- Active connection time
- Bandwidth provided
- Geographic location (rare locations earn more)
- IP rotation frequency
- Client ratings

Rewards are paid out daily to your connected wallet.

## Security Considerations

- Use a dedicated Raspberry Pi for your node
- Consider using a separate network if possible
- Keep your node software updated
- Never share your private keys
- Regularly check for suspicious activities

## Upgrading Your Node

To upgrade the node software:

```bash
nebek-node upgrade
```

Or manually:

```bash
cd node-client
git pull
cargo build --release
sudo systemctl stop nebek-node
sudo cp target/release/nebek-node /usr/local/bin/
sudo systemctl start nebek-node
```

## Community Support

- Join our Discord: https://discord.gg/nebek
- Telegram group: https://t.me/nebek_network
- Visit our forum: https://forum.nebek.network
- GitHub issues: https://github.com/nebek/node-client/issues

Thank you for joining the NEBEK network!