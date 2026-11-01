# 🐝 11 — Docker Swarm Commands (Clusters, Services, Stacks, Secrets)

> Swarm is Docker's built-in orchestrator. Examples use a service `web`, a stack `mystack`, and a node `node1`.

## 🏁 Swarm Setup

```bash
# Initialize a swarm on this machine (it becomes a manager)
docker swarm init
# Initialize with an explicit IP
docker swarm init --advertise-addr 192.168.1.10
# Show the command to join a worker
docker swarm join-token worker
# Show the command to join a manager
docker swarm join-token manager
# Join as a worker (run on the other machine)
docker swarm join --token <token> 192.168.1.10:2377
# Rotate the worker join token
docker swarm join-token --rotate worker
# Change swarm settings (e.g. certificate expiry)
docker swarm update --cert-expiry 720h
# Show the key used to unlock an auto-locked swarm
docker swarm unlock-key
# Unlock a manager after a restart
docker swarm unlock
# Show the swarm root CA
docker swarm ca
# Leave the swarm (worker)
docker swarm leave
# Leave the swarm (manager)
docker swarm leave --force
```

## 🖥️ Nodes

```bash
# List nodes
docker node ls
# Node details
docker node inspect node1
# Readable node details
docker node inspect --pretty node1
# Tasks running on a node
docker node ps node1
# Promote a worker to manager
docker node promote node1
# Demote a manager to worker
docker node demote node1
# Drain a node (move its tasks elsewhere)
docker node update --availability drain node1
# Make a node active again
docker node update --availability active node1
# Add a label to a node
docker node update --label-add zone=a node1
# Remove a node from the swarm
docker node rm node1
```

## 🚀 Services

```bash
# Create a service with 3 replicas
docker service create --name web --replicas 3 -p 80:80 nginx
# Create a global service (one task per node)
docker service create --name agent --mode global alpine sleep 3600
# Create a service on an overlay network
docker service create --name web --network myoverlay nginx
# Create a service with resource limits
docker service create --name web --limit-memory 256m --limit-cpu 0.5 nginx
# Create a service with a mounted volume
docker service create --name web --mount type=volume,source=myvol,target=/data nginx
# Create a service on a specific node role
docker service create --name web --constraint 'node.role==worker' nginx
# List services
docker service ls
# Tasks (containers) of a service
docker service ps web
# Service details
docker service inspect web
# Readable service details
docker service inspect --pretty web
# Service logs
docker service logs web
# Follow service logs
docker service logs -f web
# Scale a service
docker service scale web=5
# Update the service image (rolling update)
docker service update --image nginx:1.27 web
# Change replicas
docker service update --replicas 4 web
# Roll the update out slowly
docker service update --update-parallelism 1 --update-delay 10s --image nginx:1.27 web
# Publish a new port
docker service update --publish-add 8080:80 web
# Roll back to the previous version
docker service rollback web
# Remove a service
docker service rm web
```

## 📚 Stacks (Compose Files on Swarm)

```bash
# Deploy a stack from a compose file
docker stack deploy -c docker-compose.yml mystack
# Deploy and pull from a private registry
docker stack deploy --with-registry-auth -c docker-compose.yml mystack
# List stacks
docker stack ls
# Services in a stack
docker stack services mystack
# Tasks in a stack
docker stack ps mystack
# Remove a stack
docker stack rm mystack
```

## 🔑 Secrets

```bash
# Create a secret from a file
docker secret create db_password ./password.txt
# Create a secret from stdin
echo "s3cret" | docker secret create db_password -
# List secrets
docker secret ls
# Secret metadata (the value is never shown)
docker secret inspect db_password
# Give a service access to a secret (appears at /run/secrets/db_password)
docker service create --name db --secret db_password -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/db_password mysql
# Remove a secret
docker secret rm db_password
```

## ⚙️ Configs

```bash
# Create a config from a file
docker config create nginx_conf ./nginx.conf
# List configs
docker config ls
# Show a config
docker config inspect --pretty nginx_conf
# Give a service a config
docker service create --name web --config source=nginx_conf,target=/etc/nginx/nginx.conf nginx
# Remove a config
docker config rm nginx_conf
```

## 🌐 Overlay Networks

```bash
# Create an overlay network for services
docker network create -d overlay myoverlay
# Overlay network that standalone containers can join
docker network create -d overlay --attachable myoverlay
# Encrypted overlay network
docker network create -d overlay --opt encrypted myoverlay
```

---

⬅️ Previous: [10 — Security](10-security.md) · [Index](README.md) · ➡️ Next: [12 — Context, Plugins and Misc](12-context-plugins-misc.md)
