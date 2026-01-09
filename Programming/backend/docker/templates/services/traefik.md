#docker #tools #service

современный прокси с автонастройкой

```yml
traefik:
  image: traefik:v2.10
  ports:
    - "80:80"
    - "443:443"
  volumes:
    - /var/run/docker.sock:/var/run/docker.sock
    - ./traefik.yml:/etc/traefik/traefik.yml
```