# Load Balancer

Ce projet montre un load balancer HTTP avec HAProxy et deux serveurs web :

```text
client
  |
HAProxy :80
  |-- serveur1 :80
  `-- serveur2 :80
```

Les pages `serveur1/a.html` et `serveur2/a.html` permettent d'identifier le
serveur qui a répondu. HAProxy distribue les requêtes à tour de rôle avec
l'algorithme `roundrobin`.

## Lancer le projet

Les trois conteneurs doivent être attachés au même réseau Docker :

```bash
docker network create loadbalancer

docker run -d --name serveur1 --network loadbalancer \
  -v "$PWD/serveur1:/usr/share/nginx/html:ro" nginx:alpine
docker run -d --name serveur2 --network loadbalancer \
  -v "$PWD/serveur2:/usr/share/nginx/html:ro" nginx:alpine
docker run --rm --name haproxy --network loadbalancer -p 8080:80 \
  -v "$PWD/haproxy/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro" \
  haproxy:alpine
```

Puis ouvrir <http://localhost:8080/a.html>. Arrêter les conteneurs avec
`docker rm -f haproxy serveur1 serveur2`.
