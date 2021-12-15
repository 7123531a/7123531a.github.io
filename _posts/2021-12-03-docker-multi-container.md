
registry mirrors to accelerate access

daemon.json

```docker
{
"registry-mirrors": [
    "https://registry.docker-cn.com",
    "http://hub-mirror.c.163.com",
    "https://docker.mirrors.ustc.edu.cn"
  ],
    "log-level":        "error",
    "storage-driver":   "overlay2"
}


```



all containers have to be on the same network.

```docker
docker network create redis

docker run -it --rm --name redis-search-2 --network redis --network-alias redis-server -p 6379:6379  redislabs/redisearch:2.0.2


docker run -it --network redis redis redis-cli -h redis-server
```
