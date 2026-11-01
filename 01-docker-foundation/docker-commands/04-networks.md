# 🌐 04 — Network Commands

> Examples use `mynet` (network), `web` and `db` (containers).

## 📋 List and Inspect

```bash
# List networks (bridge, host, none exist by default)
docker network ls
# Only network IDs
docker network ls -q
# Do not truncate IDs
docker network ls --no-trunc
# Only bridge networks
docker network ls --filter driver=bridge
# Networks by name
docker network ls --filter name=my
# Custom format
docker network ls --format "table {{.Name}}\t{{.Driver}}\t{{.Scope}}"
# Full network details (subnet, gateway, connected containers)
docker network inspect mynet
# Only the subnet
docker network inspect --format '{{range .IPAM.Config}}{{.Subnet}}{{end}}' mynet
# Only the containers on the network
docker network inspect --format '{{range .Containers}}{{.Name}} {{end}}' mynet
# Inspect the default bridge
docker network inspect bridge
```

## ➕ Create

```bash
# Create a user-defined bridge network
docker network create mynet
# Specify the driver
docker network create --driver bridge mynet
# Custom subnet and gateway
docker network create --subnet 172.20.0.0/16 --gateway 172.20.0.1 mynet
# Limit the IP range handed to containers
docker network create --subnet 172.20.0.0/16 --ip-range 172.20.240.0/20 mynet
# Internal network (no outside access)
docker network create --internal mynet
# Add a label
docker network create --label env=dev mynet
# Overlay network for Swarm (standalone containers can attach)
docker network create -d overlay --attachable myoverlay
# Macvlan network (container gets an address on the LAN)
docker network create -d macvlan --subnet 192.168.1.0/24 --gateway 192.168.1.1 -o parent=eth0 mymacvlan
# Network with a custom MTU
docker network create -o com.docker.network.driver.mtu=1400 mynet
```

## 🔗 Connect Containers

```bash
# Start a container on a network
docker run -d --network mynet --name web nginx
# Start a second container on the same network
docker run -d --network mynet --name db -e POSTGRES_PASSWORD=pass postgres
# Connect a running container to a network
docker network connect mynet web
# Connect with a fixed IP
docker network connect --ip 172.20.0.50 mynet web
# Connect with an alias
docker network connect --alias site mynet web
# Disconnect a container from a network
docker network disconnect mynet web
# Force-disconnect
docker network disconnect -f mynet web
```

## 🧪 Test Connectivity

```bash
# Ping one container from another by name
docker exec web ping -c 3 db
# Resolve a container name (DNS)
docker exec web nslookup db
# Call another container's HTTP service
docker exec web curl -s http://db:5432
# Show a container's hosts file
docker exec web cat /etc/hosts
# Show a container's DNS config
docker exec web cat /etc/resolv.conf
# Show a container's network interfaces
docker exec web ip addr
# Show a container's routes
docker exec web ip route
# Check which ports a container is listening on
docker exec web ss -tulpn
```

## 🔌 Port Publishing

```bash
# Host 8080 -> container 80
docker run -d -p 8080:80 nginx
# Bind only to localhost
docker run -d -p 127.0.0.1:8080:80 nginx
# UDP port
docker run -d -p 5353:53/udp alpine sleep 3600
# Both TCP and UDP
docker run -d -p 53:53/tcp -p 53:53/udp alpine sleep 3600
# Show mappings of a container
docker port web
```

## 🏠 Host and None Networks

```bash
# Share the host network stack (no port mapping needed)
docker run -d --network host nginx
# Disable networking completely
docker run -d --network none alpine sleep 3600
# Share another container's network namespace
docker run -d --network container:web alpine sleep 3600
```

## 🖧 On the Host (Linux)

```bash
# Show the docker0 bridge interface
ip addr show docker0
# List the bridge links
ip link show type bridge
# See the NAT rules Docker created
sudo iptables -t nat -L -n
# See Docker's firewall chains
sudo iptables -L DOCKER-USER -n
# Check which host ports are in use
sudo ss -tulpn
```

## 🧹 Remove

```bash
# Remove a network
docker network rm mynet
# Remove several networks
docker network rm net1 net2
# Remove all unused networks
docker network prune
# Prune without confirmation
docker network prune -f
# Prune networks unused for 24 hours
docker network prune --filter "until=24h"
```

---

⬅️ Previous: [03 — Containers](03-containers.md) · [Index](README.md) · ➡️ Next: [05 — Volumes](05-volumes.md)
