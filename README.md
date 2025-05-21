# Pipe Network Node Run Full Guide for New and Old users  (VPS & PC users)


## Prerequisites (Devnet Node Run Users Only)
	
## Stop your Old Node


## 1. Backup `Node ID`

```
nano ~/node_info.json
```

## 2. Stop old node
```
sudo systemctl stop pipe
sudo systemctl disable pipe
sudo systemctl daemon-reload
```

---

# Dependencies for WINDOWS & LINUX & VPS

```
sudo apt-get update && sudo apt-get upgrade -y
```
```
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev  -y
```
```
sudo apt install -y libssl-dev ca-certificates
```

# Enable Firewall & Open Ports

```
sudo ufw allow ssh
sudo ufw enable

sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw reload
```

# Create System Configuration
```
sudo bash -c 'cat > /etc/sysctl.d/99-popcache.conf << EOL
net.ipv4.ip_local_port_range = 1024 65535
net.core.somaxconn = 65535
net.ipv4.tcp_low_latency = 1
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.core.wmem_max = 16777216
net.core.rmem_max = 16777216
EOL'
sudo sysctl -p /etc/sysctl.d/99-popcache.conf
```
```
sudo bash -c 'cat > /etc/security/limits.d/popcache.conf << EOL
*    hard nofile 65535
*    soft nofile 65535
EOL'
```
# Now Restart Your terminal(for all)

# Make Directory (create folder to be used for download cache)

```
sudo mkdir -p /opt/popcache
sudo mkdir -p /opt/popcache/logs
```
```
cd /opt/popcache
```

# For VPS Only to run in Background

```
apt install screen -y
```
```
screen -S pipe
```
- PRESS CTRL+A+D (to run ur node continuously)
- To check ur Node Again
```
screen -r pipe
```

# Important for Pipe Binaries
## Visit: https://download.pipe.network  & To download file (You will need Invite code from email Those who hav not recieved yet fill the form)

1.For Local PC 

```
sudo cp -r /mnt/c/Users/bhaty/Downloads/File /opt/popcache/
```
Replace "c/Users/bhaty/Downloads/File" to your Actual File Path in ur System

2. For VPS (Run in Command Prompt or use termius drag and drop as shown in my previous clip posted on my X)

```
scp -r "C:\Users\bhaty\Downloads\File" root@VPS_IP:/opt/popcache/
```
Replace "C:\Users\bhaty\Downloads\File" to your Actual File Path in ur System and VPS_IP with ur Actual VPS IP
```
sudo tar -xzf pop-v0.3.0-linux-*.tar.gz
```
```
sudo chmod +x /opt/popcache/pop
```

# Setup Config File

## Get the locations of your PC or VPS and Copy it to Notes

- `pop-location`: location of PC or VPS --> Command to Check --> `realpath --relative-to /usr/share/zoneinfo /etc/localtime`
- `website`: Anything you prefer (TG, X, Or any Link)


```
nano config.json
```

```
{
  "pop_name": "your-name",
  "pop_location": "Your or VPS Location, Country",
  "invite_code": "Enter Mail Invite Code",
  "server": {
    "host": "0.0.0.0",
    "port": 443,
    "http_port": 80,
    "workers": 0
  },
  "cache_config": {
    "memory_cache_size_mb": 12288,
    "disk_cache_path": "./cache",
    "disk_cache_size_gb": 250,
    "default_ttl_seconds": 86400,
    "respect_origin_headers": true,
    "max_cacheable_size_mb": 1024
  },
  "api_endpoints": {
    "base_url": "https://dataplane.pipenetwork.com"
  },
  "identity_config": {
    "node_name": "your-node-name",
    "name": "Your Name",
    "email": "your.email@example.com",
    "website": "https://your-website.com",
    "discord": "your_discord_username",
    "telegram": "your_telegram_handle",
    "solana_pubkey": "YOUR_SOLANA_WALLET_ADDRESS_FOR_REWARDS"
  }
}
```
Then save - CTRL+X Then Enter "Y" Then Enter




# Creating a Systemd Service File

```
sudo bash -c 'cat > /etc/systemd/system/popcache.service << EOL
[Unit]
Description=POP Cache Node
After=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/opt/popcache
ExecStart=/opt/popcache/pop
Restart=always
RestartSec=5
LimitNOFILE=65535
StandardOutput=append:/opt/popcache/logs/stdout.log
StandardError=append:/opt/popcache/logs/stderr.log
Environment=POP_CONFIG_PATH=/opt/popcache/config.json

[Install]
WantedBy=multi-user.target
EOL'
```

# Run Node

```
sudo systemctl daemon-reload
```
```
sudo systemctl enable popcache
```
```
sudo systemctl start popcache
```

# Open Another Window for WSL or VPS

## Monitor your Node Status & Logs
```
sudo systemctl status popcache
```
```
sudo journalctl -u popcache
```


# Next Day Run This Command to Restart

## For All PC/VPS

```
cd /opt/popcache
```
```
sudo systemctl daemon-reload
```
```
sudo systemctl enable popcache
```
```
sudo systemctl start popcache
```

### To Stop Node

```
sudo systemctl stop popcache
```

## To Restart
```
cd /opt/popcache
sudo systemctl daemon-reload
sudo systemctl enable popcache
sudo systemctl restart popcache
```

# TROUBLESHOOTING ✅
## Need to Free Your 8003 Port

## Identify the Process Using Port 443
```
sudo ss -tulpn | grep 443
```

Example - ``` LISTEN  0  128  0.0.0.0:0380  0.0.0.0:*  users:(("nginx",pid=1234,fd=6)) ```

## Terminate the Process by PID
```
sudo kill -9 123
```

## Kill All Processes Using Port 8003
```
sudo fuser -k 443/tcp
```

Reference Video How to Free 8003 or any port - https://youtu.be/4iP4GvLfCrU?t=229

# Delete Pipe node to Reset

```
cd /data
sudo rm -rf /opt/popcache  # Deletes the 'popcache' binary
sudo rm -f config.json  # Deletes the 'node_info.json' file
```
