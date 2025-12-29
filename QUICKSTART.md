# Quick Start Guide - Portfolio Scalable Application

## 🚀 Fast Deployment (5 Minutes)

### Prerequisites Check
```bash
# Check Docker
docker --version  # Should be 20.10+

# Check Docker Compose
docker-compose --version  # Should be 2.0+
```

### Step 1: Download & Setup (1 min)
```bash
# Clone repository
git clone https://github.com/yourusername/portfolio-app.git
cd portfolio-app

# Setup environment
cp .env.example .env

# Edit passwords (IMPORTANT!)
nano .env
# Change: DB_PASSWORD, REDIS_PASSWORD
```

### Step 2: Start Application (2 min)
```bash
# Start all services
docker-compose up -d

# Wait for services to be healthy (30-60 seconds)
docker-compose ps

# Expected output:
# NAME                    STATUS
# portfolio_nginx         Up (healthy)
# portfolio_backend_1     Up (healthy)
# portfolio_backend_2     Up (healthy)
# portfolio_backend_3     Up (healthy)
# portfolio_db            Up (healthy)
# portfolio_redis         Up (healthy)
```

### Step 3: Verify (1 min)
```bash
# Test health endpoint
curl http://localhost/health
# Expected: {"status":"healthy","timestamp":"...","uptime":...}

# Test API
curl http://localhost/api/profile
# Expected: JSON response with profile data

# Open in browser
# http://localhost
```

### Step 4: Start Monitoring (Optional, 1 min)
```bash
# Start monitoring stack
docker-compose -f docker-compose.monitoring.yml up -d

# Access Grafana
# http://localhost:3002
# Username: admin
# Password: admin

# Access Prometheus
# http://localhost:9090
```

## 📊 What You Get

✅ **3 Backend API instances** (load balanced)
✅ **PostgreSQL database** (with sample data)
✅ **Redis cache** (for high performance)
✅ **Nginx load balancer** (with health checks)
✅ **Auto-restart** on failures
✅ **Health monitoring**

## 🎯 Common Operations

### Scale Backend Instances
```bash
# Scale to 5 instances
docker-compose up -d --scale backend_1=5

# Check running instances
docker-compose ps | grep backend
```

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f backend_1

# Last 100 lines
docker-compose logs --tail=100 backend_1
```

### Restart Services
```bash
# Restart all
docker-compose restart

# Restart specific service
docker-compose restart backend_1
```

### Stop Application
```bash
# Stop all services
docker-compose down

# Stop and remove volumes (CAUTION: Deletes data!)
docker-compose down -v
```

## 🧪 Performance Testing

### Simple Load Test
```bash
# Install Apache Bench (if needed)
sudo apt-get install apache2-utils  # Ubuntu/Debian
brew install httpd  # macOS

# Run test: 1000 requests, 100 concurrent
ab -n 1000 -c 100 http://localhost/api/profile

# Expected results:
# Requests per second: >500 (without cache)
# Requests per second: >2000 (with cache)
# Failed requests: 0
```

### Check Cache Performance
```bash
# First request (cache miss)
time curl http://localhost/api/profile

# Second request (cache hit - should be faster)
time curl http://localhost/api/profile

# Check Redis cache
docker exec portfolio_redis redis-cli
> INFO stats
> KEYS *
```

## 📈 Monitoring Dashboard

### Grafana Dashboards
1. Open http://localhost:3002
2. Login: admin/admin
3. Navigate to Dashboards
4. View:
   - **API Performance**: Response times, throughput
   - **System Resources**: CPU, Memory, Disk
   - **Database**: Queries, connections
   - **Cache**: Hit rate, operations

### Key Metrics to Watch
- **Response Time**: Should be <100ms (P95)
- **Error Rate**: Should be <0.1%
- **CPU Usage**: Should be <70% per container
- **Cache Hit Rate**: Should be >80%
- **Database Connections**: Should be <50% of max

## 🐛 Troubleshooting

### Service Won't Start
```bash
# Check logs
docker-compose logs [service_name]

# Common issues:
# 1. Port already in use
sudo lsof -i :80  # Check port 80
sudo lsof -i :5432  # Check port 5432

# 2. Permission issues
sudo chown -R $USER:$USER .
```

### Database Connection Errors
```bash
# Check database is running
docker-compose ps postgres

# Check database logs
docker-compose logs postgres

# Verify credentials in .env match docker-compose.yml
```

### High CPU Usage
```bash
# Check resource usage
docker stats

# Scale up if needed
docker-compose up -d --scale backend_1=5
```

### Cache Not Working
```bash
# Check Redis
docker exec portfolio_redis redis-cli ping
# Expected: PONG

# Check cache keys
docker exec portfolio_redis redis-cli KEYS "*"

# Clear cache if needed
docker exec portfolio_redis redis-cli FLUSHALL
```

## 🔒 Security Checklist

Before going to production:

- [ ] Change all default passwords in `.env`
- [ ] Enable HTTPS/SSL in Nginx
- [ ] Configure firewall rules
- [ ] Set up backup for PostgreSQL
- [ ] Enable Redis persistence
- [ ] Configure monitoring alerts
- [ ] Review rate limiting settings
- [ ] Set up log aggregation

## 📚 Next Steps

1. **Customize Content**: Update data in PostgreSQL
2. **Add Domain**: Configure your domain in Nginx
3. **Enable SSL**: Set up Let's Encrypt certificates
4. **Set Alerts**: Configure AlertManager for incidents
5. **Backup Strategy**: Set up automated database backups

## 💡 Pro Tips

### Optimize for Production
```bash
# Increase backend instances
docker-compose up -d --scale backend_1=5

# Monitor performance
docker stats

# Check Nginx access logs
docker exec portfolio_nginx tail -f /var/log/nginx/access.log
```

### Database Optimization
```bash
# Access database
docker exec -it portfolio_db psql -U postgres -d portfolio_db

# Analyze slow queries
SELECT * FROM pg_stat_statements 
ORDER BY total_time DESC LIMIT 10;

# Create additional indexes if needed
CREATE INDEX idx_custom ON table_name(column_name);
```

### Cache Tuning
```bash
# Monitor Redis
docker exec portfolio_redis redis-cli INFO stats

# Adjust TTL in backend/server.js
# Increase for static data, decrease for dynamic data
```

## 🎓 Learning Resources

- [Docker Documentation](https://docs.docker.com/)
- [PostgreSQL Best Practices](https://wiki.postgresql.org/wiki/Performance_Optimization)
- [Redis Caching Strategies](https://redis.io/docs/manual/patterns/)
- [Nginx Load Balancing](https://nginx.org/en/docs/http/load_balancing.html)

## 🆘 Get Help

- GitHub Issues: [Report a bug](https://github.com/yourusername/portfolio-app/issues)
- Email: alex.chen@email.com
- Documentation: See README.md for detailed info

---

**You're now running a production-grade, scalable portfolio application! 🎉**
