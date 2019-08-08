stop all processes
```
docker stop $(docker ps -aq) -f
```

remove all containers
```
docker rm $(docker ps -aq) -f
```

remove all images
```
docker rmi $(docker images -aq) -f
```

remove all stopped containers, all dangling images, all unused networks and all unused volumes
```
docker system prune --volumes
```
