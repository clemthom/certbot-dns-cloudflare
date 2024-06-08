# Creating SSL certs using certbot dns challenge with cloudflare dns. 

* docker run commands to issue / view / renew ssl certs using certbot dns challenge with cloudflare dns
* docker compose nginx configs to quickly test the issued certifcates

## Setting it up
* Create cloudflare API Token with `Zone:DNS:Edit` permissions for the zones you need certificates for and add the same in `cloudflare-secret.ini`

### Obtain a new certificate

* Replace the `EMAIL` and `DOMAIN` in the below command.  

```
docker run \
    -v $(pwd)/data/certbot/conf:/etc/letsencrypt \
    -v $(pwd)/cloudflare-secret.ini:/etc/.cloudflare-secret.ini \
    certbot/dns-cloudflare:v2.10.0 \
    certonly --dns-cloudflare -n \
    --dns-cloudflare-credentials /etc/.cloudflare-secret.ini \
    --email EMAIL \
    -d DOMAIN \
    --agree-tos
```

### View certificates managed by certbot

```
docker run \
    -v $(pwd)/data/certbot/conf:/etc/letsencrypt \
    -v $(pwd)/cloudflare-secret.ini:/etc/.cloudflare-secret.ini \
    certbot/dns-cloudflare:v2.10.0 certificates
```

### Renew certificates

```

docker run \
    -v $(pwd)/data/certbot/conf:/etc/letsencrypt \
    -v $(pwd)/cloudflare-secret.ini:/etc/.cloudflare-secret.ini \
    certbot/dns-cloudflare:v2.10.0 \
    renew
```

### Check the newly issued certificate with nginx container

* Modify `data/nginx/app.conf` and replace `DOMAIN`
* add hosts entry for `DOMAIN` to `127.0.0.1`
* docker compose up

```
docker compose up
```

* now you should be able to see a `Hello world` when you go to `https://DOMAIN`

### Proxy pass nginx to a port in host machine

* Modify `data/nginx/proxy_pass.conf` and replace `DOMAIN` and also port in `proxy_pass http://host.docker.internal:8080/;` if required
* add hosts entry for `DOMAIN` to `127.0.0.1`
* docker compose up

```
docker compose -f docker-compose-proxy-pass.yml up
```

* now you should be able to see nginx proxy passing requests to an app running in host machine, when you go to `https://DOMAIN`

## References
* https://github.com/wmnnd/nginx-certbot
* https://certbot-dns-cloudflare.readthedocs.io/en/stable/

## License
All code in this repository is licensed under the terms of the `MIT License`. For further information please refer to the `LICENSE` file.
