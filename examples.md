## Common Containers Examples

#### 1) Adding Redis Server
Open up `docker-compose.yml` file, and add the following block.
```yaml
# Redis server container
  redis:
    image: redis:latest
    container_name: redis
    ports:
      - 6379:6379
    networks:
      - app-network
```
Customize as needed and you're done!!  
Access it in your container via `redis` as the host
