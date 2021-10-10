docker pull nginx

docker run --name nginx-http2 -d -v /home/eric/nginx-http2-load-balance/nginx.conf:/etc/nginx/nginx.conf  -p 9000:9000 nginx

```nginx

upstream myapp1 {
    server 172.17.0.1:5051;
    server 172.17.0.1:5052;
}

server {
    listen 9000 http2;

    location / {
        grpc_pass grpc://myapp1;
    }
}

```

docker logs

docker exec -i -t <container name>