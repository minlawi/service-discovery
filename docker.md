docker pull nyanlintun/counting:latest
docker pull nyanlintun/dashboard:latest
docker pull hashicorp/consul:latest

docker run --rm \
  --name counting-api \
  --network ace \
  -e PORT=9001 \
  -p 9001:9001 \
  nyanlintun/counting:latest

docker run --rm \
  --name dashboard-ui \
  --network ace \
  -p 9000:9000 \
  -e PORT=9000 \
  -e COUNTING_API_URL=http://counting-api:9001 \
  nyanlintun/dashboard:latest

docker run --rm \
--name consul \
--hostname consul \
-p 8500:8500 \
-p 8600:8600/tcp \
-p 8600:8600/udp \
-v ./consul-config:/consul-config \
--network ace \
hashicorp/consul:latest \
consul agent -dev -server -ui \
-client=0.0.0.0 \
-config-dir=/consul-config \
-log-level=info

docker run --rm \
  --name dashboard-ui-2 \
  --network ace \
  -p 9002:9000 \
  -e PORT=9000 \
  -e COUNTING_API_URL=http://counting-api:9001 \
  nyanlintun/dashboard:latest

docker run --rm \
--name dashboard-nginx \
-p 8080:80 \
-v ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro \
--network ace \
nginx:latest \
nginx -g 'daemon off;'
