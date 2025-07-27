# Nginx Reverse Proxy and Let's Encrypt with Docker Compose

This repository shows a way to get a free Let's Encrypt certificate for nginx. A reverse proxy is a server that sits in front of other servers and handles requests from clients for them. Each request is forwarded to the respective server, and the response is then sent back to the client.

The certbot utility runs from a Docker Container and is employed to get the Let's Encrypt certificate. The certbot manual can be found [here](https://eff-certbot.readthedocs.io/).


## Installation

Clone this repository to your local computer:

```bash
git clone https://github.com/contaware/docker-revproxy-certbot.git
```


## Configuration 

In *./http/default.conf* and *./https/default.conf* set your domain(s) and the proxied servers.

To start cleanly, delete the *./certbot/* directory if it already exists.


## Get Certificate

1. Launch the Docker Containers; at this point, only the http server will run, as the https server does not yet have a certificate:

   ```bash
   docker compose up -d
   ```

2. Make sure that your Docker Host is accessible from the outside through port **80** and run a certbot test:

   ```bash
   docker compose run --rm certbot certonly --webroot --webroot-path /var/www/certbot --dry-run --cert-name mycerts -d app1.example.com -d app2.example.com
   ```

3. If successful, issue your certificate without the `--dry-run` option:

   ```bash
   docker compose run --rm certbot certonly --webroot --webroot-path /var/www/certbot --cert-name mycerts -d app1.example.com -d app2.example.com
   ```

   Certificate and Key are saved to:

   ```bash
   /etc/letsencrypt/live/mycerts/fullchain.pem
   /etc/letsencrypt/live/mycerts/privkey.pem
   ```


## Run Reverse Proxy

Make sure that your Docker Host is accessible from the outside through port **443** and restart the Docker Containers; if the certificate has been issued correctly, the https server should start:

```bash
docker compose down
docker compose up -d
```


## Renew Certificate

Remember to renew the certificate every 3 month:

```bash
docker compose run --rm certbot renew
```

Nginx must be reloaded to apply the updated certificate:

```bash
docker compose exec https nginx -s reload
```


## Alternatives

If you prefer, there is also a convenient Docker Image with a web interface that automates the process outlined here, check [Nginx Proxy Manager](https://nginxproxymanager.com/).
