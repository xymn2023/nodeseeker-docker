# nodeseeker-docker
Nodeseek论坛关键字监控机器人Docker版。



docker-compose.yml

```
version: '3.8'
services:
  nodeseeker:
    image: smhw3565/nodeseeker-docker:latest  
    container_name: nodeseeker-app
    restart: unless-stopped
    ports:
      - "3010:3010"
    environment:
      - NODE_ENV=production
      - PORT=3010
      - HOST=0.0.0.0
      - DATABASE_PATH=/usr/src/app/data/nodeseeker.db
      - RSS_URL=${RSS_URL:-https://rss.nodeseek.com/}
      - CORS_ORIGINS=${CORS_ORIGINS:-http://localhost:3010}
    volumes:
      # 持久化数据库文件
      - nodeseeker_data:/usr/src/app/data
      # 日志目录
      - nodeseeker_logs:/usr/src/app/logs
    networks:
      - nodeseeker_network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3010/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
volumes:
  nodeseeker_data:
    driver: local
  nodeseeker_logs:
    driver: local
networks:
  nodeseeker_network:
    driver: bridge
```



.env配置


```
# Server Configuration
PORT=3010
HOST=0.0.0.0
NODE_ENV=development
CORS_ORIGINS=http://localhost:3010             

# Database Configuration
DATABASE_PATH=./data/nodeseeker.db

# JWT 密钥
JWT_SECRET=必填内容


# RSS Configuration
RSS_URL=https://rss.nodeseek.com/
RSS_TIMEOUT=30000
RSS_USER_AGENT=NodeSeeker-Bot/1.0
RSS_CHECK_ENABLED=true
RSS_CRON_EXPRESSION=*/1 * * * *

# Data Cleanup
DATA_CLEANUP_ENABLED=true
CLEANUP_CRON_EXPRESSION=0 2 * * *
DATA_RETENTION_DAYS=30

# Logging
LOG_LEVEL=info
```


特别说明：.env配置中JWT_SECRET为必填内容，Webhook必须设置https才能正常访问,否则就算配置Bot Token配置也不通过。



[Docker Hub](https://hub.docker.com/r/smhw3565/nodeseeker-docker)
