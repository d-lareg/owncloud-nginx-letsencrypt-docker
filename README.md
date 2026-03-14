# owncloud-nginx-letsencrypt-docker

This repository provides a `docker-compose.yml` configuration to run [ownCloud](https://owncloud.org/) on ARM-based systems, such as the Raspberry Pi. The setup includes an Nginx proxy with Let's Encrypt integration. Instead of using volumes to store the data, every data is stored in local folders on disc.

## Information sources

This is consolidated based on information from the following places and thanks to them:
- [ownCloud server repository](https://github.com/owncloud-docker/server)
- [LetsEncrypt NGINX Proxy companion](https://hub.docker.com/r/jrcs/letsencrypt-nginx-proxy-companion/)
- [Tutorial: Docker + Nginx + Let's Encrypt](https://gilyes.com/docker-nginx-letsencrypt/)

## Get started

Pretty straightforward, follow these steps...

Set up the necessary environment variables at the command line (or equivalent method on the relevant operating system):

```bash
cat << EOF >| .env
OWNCLOUD_VERSION=10.16.1
OWNCLOUD_DOMAIN=localhost
ADMIN_USERNAME=admin
ADMIN_PASSWORD=admin
HTTP_PORT=8080
LETSENCRYPT_EMAIL=x@x.x
EOF
```

The webserver is a nginx-proxy and it will listen on ports 80 and 443.

The domain associated with the certificate will be the `OWNCLOUD_DOMAIN` you defined in the `.env` file.

And then run docker compose up to get going.

```bash
docker-compose up -d
```

You should then be able to access it at the domain name you entered and it will redirect to the https URL with a valid certificate.

In case you have to restart your system you can stop the instance with:

```bash
docker-compose stop
```

And restart it with:

```bash
docker-compose start
```

## Backup

As all data is stored in folders instead of valumes, it is easy to backup your data.

Stop your instance:

```bash
docker-compose down
```

Generate a compressed backup:

```bash
docker run --rm -v $(pwd):/owncloud ubuntu tar czfv /owncloud/files_$(date +'%F').tar /owncloud/owncloud/
```


## Upgrade your ownCloud

To upgrade your instance follow the official [Guide](https://doc.owncloud.com/server/next/admin_manual/installation/docker/#upgrading-owncloud-on-docker). But most likely you have to do the following steps:

1. ```docker-compose exec owncloud occ maintenance:mode --on```
2. ```docker-compose down```
3. Edit the version in your ```.env``` file
4. ```docker-compose up```
5. ```docker-compose exec owncloud occ maintenance:mode --off```

If you encounter a time out error after the new image has been downloaded, simply execute step 4. and 5. once again.