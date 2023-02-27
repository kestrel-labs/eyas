# eyas
Mobile base host configuration

1. Install Ubuntu 22.04
1. Install vim
```
sudo apt-get update
sudo apt-get install -y vim
```
1. Enable remote ssh
```
sudo apt-get install -y openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

1. Set static ip
To see current ip
```
ip addr show
```
To set static ip address
```
sudo vim /etc/netplan/01-netcfg.yaml
```
add
```
network:
  version: 2
  renderer: networkd
  ethernets:
    enp1s0: # Edit this line according to your network interface name.
      dhcp4: no
      addresses:
        - 192.168.50.252/24 # Edit this line according to your host ip.
      routes:
        - to: default
          via: 192.168.50.1 # Edit this line according to your router ip.
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
```
sudo netplan apply
reboot
```

1. Add to your `/etc/hosts`
```
192.168.50.252  kestrel-runner1
```

1. Install docker and compose
https://docs.docker.com/engine/install/ubuntu/
```
sudo apt-get update
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

1. Set up systemd unit
`sudo vim /etc/systemd/system/docker-compose@.service`
```
[Unit]
Description=%i service with docker compose
PartOf=docker.service
After=docker.service

[Service]
Type=oneshot
RemainAfterExit=true
WorkingDirectory=/etc/docker/compose/%i
ExecStart=/usr/bin/docker compose up -d --remove-orphans
ExecStop=/usr/bin/docker compose down

[Install]
WantedBy=multi-user.target
```
