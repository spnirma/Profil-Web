# Portfolio Application - Scalable Architecture

A highly scalable, production-ready portfolio application with load balancing, caching, and auto-scaling capabilities.

## 🏗️ Architecture Overview

```
┌─────────────┐
│   Clients   │
└──────┬──────┘
       │
┌──────▼──────────┐
│  Nginx (LB)     │  ← Load Balancer + Reverse Proxy
└──────┬──────────┘
       │
   ┌───┴───┬───────┬───────┐
   │       │       │       │
┌──▼───┐ ┌─▼────┐ ┌─▼────┐ │
│ API  │ │ API  │ │ API  │ ... (Auto-scaled)
│ Pod1 │ │ Pod2 │ │ Pod3 │ │
└──┬───┘ └─┬────┘ └─┬────┘ │
   │       │        │      │
   └───┬───┴────┬───┴──────┘
       │        │
┌──────▼─────┐ ┌▼────────┐
│ PostgreSQL │ │  Redis  │
│  Database  │ │  Cache  │
└────────────┘ └─────────┘
```

## 🚀 Features

### Scalability
- **Horizontal Pod Autoscaling**: Automatically scales based on CPU/memory usage
- **Load Balancing**: Nginx distributes traffic across multiple backend instances
- **Connection Pooling**: Efficient database connection management
- **Caching**: Redis for high-performance data caching

### Performance
- **Response Caching**: API responses cached in Redis
- **Database Indexing**: Optimized queries with proper indexes
- **Gzip Compression**: Reduced bandwidth usage
- **Connection Keep-Alive**: Reduced overhead for repeated requests

### Reliability
- **Health Checks**: Automated health monitoring
- **Graceful Shutdown**: Proper handling of SIGTERM signals
- **Circuit Breaker Pattern**: Fail-fast for unavailable services
- **Rate Limiting**: Protection against abuse

### Security
- **Helmet.js**: Security headers
- **Rate Limiting**: API request throttling
- **Input Validation**: Request validation with Joi
- **CORS**: Controlled cross-origin access

## 📋 Prerequisites

- Docker & Docker Compose
- Node.js 18+ (for local development)
- PostgreSQL 15+
- Redis 7+
- Kubernetes cluster (for K8s deployment)

## 🔧 Quick Start

### 1. Clone Repository
```bash
git clone https://github.com/yourusername/portfolio-app.git
cd portfolio-app
```

### 2. Environment Setup
```bash
cp .env.example .env
# Edit .env with your actual values
nano .env
```

### 3. Docker Compose Deployment
```bash
# Start all services
docker-compose up -d

# Check status
docker-compose ps

# View logs
docker-compose logs -f

# Scale backend instances
docker-compose up -d --scale backend=5
```

### 4. Access Application
- Frontend: http://localhost
- API: http://localhost/api
- Health Check: http://localhost/health

## 🎯 Deployment Options

### Option 1: Docker Compose (Development/Small Scale)

**Pros:**
- Easy setup
- Good for development
- Suitable for small to medium traffic

**Commands:**
```bash
# Start
docker-compose up -d

# Scale backends
docker-compose up -d --scale backend_1=3 backend_2=3

# Stop
docker-compose down
```

### Option 2: Kubernetes (Production/Large Scale)

**Pros:**
- Auto-scaling
- Self-healing
- Rolling updates
- Suitable for high traffic

**Deployment:**
```bash
# Apply configurations
kubectl apply -f k8s/deployment.yaml

# Check status
kubectl get pods -n portfolio
kubectl get hpa -n portfolio

# View logs
kubectl logs -f deployment/portfolio-backend -n portfolio

# Scale manually
kubectl scale deployment portfolio-backend --replicas=5 -n portfolio
```

## 📊 Scaling Configuration

### Horizontal Pod Autoscaler (HPA)

The application automatically scales based on:

```yaml
minReplicas: 3
maxReplicas: 10
targetCPUUtilizationPercentage: 70
targetMemoryUtilizationPercentage: 80
```

**Scaling Behavior:**
- **Scale Up**: Immediate when threshold exceeded
- **Scale Down**: 5-minute stabilization window
- **Max Scale**: 10 pods

### Load Balancing Algorithm

Nginx uses **least_conn** algorithm:
- Routes to server with fewest active connections
- Prevents overloading single instance
- Better resource utilization

### Database Connection Pooling

```javascript
max: 20 connections per instance
idleTimeout: 30 seconds
connectionTimeout: 2 seconds
```

## 🔍 Monitoring

### Health Checks

```bash
# Application health
curl http://localhost/health

# Kubernetes health
kubectl get pods -n portfolio
kubectl describe hpa portfolio-backend-hpa -n portfolio
```

### Metrics

Monitor these key metrics:
- **Request Rate**: Requests per second
- **Response Time**: Average/P95/P99 latency
- **Error Rate**: 4xx/5xx responses
- **CPU/Memory**: Resource utilization
- **Cache Hit Rate**: Redis cache efficiency

### Logs

```bash
# Docker Compose
docker-compose logs -f backend_1

# Kubernetes
kubectl logs -f deployment/portfolio-backend -n portfolio

# Nginx access logs
docker exec portfolio_nginx tail -f /var/log/nginx/access.log
```

## 🧪 Performance Testing

### Load Testing with Apache Bench

```bash
# Test with 1000 requests, 100 concurrent
ab -n 1000 -c 100 http://localhost/api/profile

# With keep-alive
ab -n 10000 -c 200 -k http://localhost/api/profile
```

### Expected Performance

| Metric | Target | With Cache |
|--------|--------|------------|
| Response Time (P95) | < 100ms | < 10ms |
| Throughput | > 1000 req/s | > 5000 req/s |
| Error Rate | < 0.1% | < 0.01% |
| Cache Hit Rate | N/A | > 80% |

## 🔐 Security Best Practices

1. **Environment Variables**: Never commit `.env` file
2. **Database**: Use strong passwords
3. **Redis**: Enable password authentication
4. **Rate Limiting**: Configure per endpoint
5. **HTTPS**: Enable SSL in production
6. **CORS**: Restrict to your domain

## 📈 Scaling Strategy

### Traffic Growth Scenarios

**0-100 req/s**: 3 backend instances (baseline)
**100-500 req/s**: 3-5 instances (HPA scales up)
**500-1000 req/s**: 5-7 instances
**1000-2000 req/s**: 7-10 instances
**2000+ req/s**: Consider adding more resources or CDN

### Database Scaling

**Vertical**: Increase PostgreSQL resources
**Horizontal**: 
- Read replicas for read-heavy workloads
- Connection pooling optimization
- Query optimization

### Cache Optimization

**Redis Strategies**:
- Increase TTL for static data
- Use Redis Cluster for > 10GB cache
- Monitor hit rate and eviction

## 🐛 Troubleshooting

### High CPU Usage
```bash
# Check pod CPU
kubectl top pods -n portfolio

# Scale up
kubectl scale deployment portfolio-backend --replicas=8 -n portfolio
```

### Database Connection Issues
```bash
# Check pool status
docker exec portfolio_db psql -U postgres -c "SELECT count(*) FROM pg_stat_activity;"

# Increase max connections if needed
```

### Cache Miss Rate High
```bash
# Check Redis status
docker exec portfolio_redis redis-cli INFO stats

# Increase TTL in .env
```

## 🚀 CI/CD Pipeline

### GitHub Actions Example

```yaml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build & Push Docker Image
        run: |
          docker build -t your-registry/portfolio-backend:${{ github.sha }} .
          docker push your-registry/portfolio-backend:${{ github.sha }}
      - name: Deploy to K8s
        run: |
          kubectl set image deployment/portfolio-backend \
            backend=your-registry/portfolio-backend:${{ github.sha }} -n portfolio
```

## 📞 Support

For issues or questions:
- GitHub Issues: https://github.com/yourusername/portfolio-app/issues
- Email: alex.chen@email.com

## 📄 License

MIT License - see LICENSE file for details

---

**Built with ❤️ for scalability and performance**
