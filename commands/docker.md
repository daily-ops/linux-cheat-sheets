## List docker containers

### Render list of containers with selective columns output.

```
docker ps -f name=a-container-name --format "table {{.Names}}\t{{.Status}}"
```
