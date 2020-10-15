# SSL
Following you can find a couple of Docker commands that are useful to add SSL to another docker container. This means that if you are running a Docker container representing a web page or a REST API, you can easily add SSL to it by executing the following commands. 

The commands are using [nginx-proxy]() and [nginx-proxy-letsencrypt]() to work, and they only require that: 

- the service Docker container exposes at least one port
- the Docker host is reachable using a domain name

## Adding SSL
The first thing we need to do is run `nginx-proxy`: 

```shell
docker run --detach     
  --name nginx-proxy \
  --publish 80:80 \
  --publish 443:443 \
  --volume /etc/nginx/certs \
  --volume /etc/nginx/vhost.d \
  --volume /usr/share/nginx/html \
  --volume /var/run/docker.sock:/tmp/docker.sock:ro \
  --restart always \
  jwilder/nginx-proxy
``` 

Then, we need to run `nginx-proxy-letsencrypt`: 

```shell
docker run --detach
  --name nginx-proxy-letsencrypt \
  --volumes-from nginx-proxy \
  --volume /var/run/docker.sock:/var/run/docker.sock:ro \
  --env "DEFAULT_EMAIL=YOUR_EMAIL" \
  --restart always \
  jrcs/letsencrypt-nginx-proxy-companion
```

**Note**  
Make sure to replace `YOUR_EMAIL` with a valid email. This will be used to contact if you the automatic renewal of the SSL certificates with LetsEncrypt does not work properly.

## Running your container
Finally, once both `nginx-proxy` and `nginx-proxy-letsencrypt` are running, you can now run your container. 

The only thing to do to make sure it works is to add a couple of environmental variables to the `run` command: 

```shell
docker run \
  --env "VIRTUAL_HOST=<YOUR_DOMAIN>" \
  --env "VIRTUAL_PORT=<EXPOSED_PORT>" \
  --env "LETSENCRYPT_HOST=<YOUR_DOMAIN>" \
  --env "LETSENCRYPT_EMAIL=<YOUR_EMAIL>" \
  ...
```

- `YOUR_DOMAIN` should be the domain at which it is possible to reach the host
- `VIRTUAL_PORT` should be the port to be used to reach the container, if it does expose more than one port
- `YOUR_EMAIL` should be a valid email address used to contact you if there is anything wrong with the setup or the automatic SSL certificate renewal process
