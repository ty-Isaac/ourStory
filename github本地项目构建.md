---
title:github项目构建方式

---

```bash
echo "# Paper-Design" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:ty-Isaac/Paper-Design.git
git push -u origin master
```





```sh
docker-machine create --driver virtualbox manager01
docker-machine ssh manager01
```



```bash
docker swarm init --advertise-addr 192.168.99.100
docker swarm join --token SWMTKN-1-4yygequxrc063rdtecgjtaw6rya4939yd6bvrwrzhunoq2iwew-219peo7wurdwydz6ko33rbneu 192.168.99.100:2377
```



```bash
docker service create --replicas 1 --name hello busybox
docker service ps 服务名
docker service logs 服务名
docker service rm 服务名
docker service scale 服务名=3
docker service ls
docker service ps -f 'desired-state=runing' hello
# 创建服务的同时暴露端口，宿主机的88端口和容器的80端口
docker service creat --name my_web -p 88:80 --replicas 3 nginx

# 查看服务详细信息
docker service inspect my_web

# 
docker network ls

```

