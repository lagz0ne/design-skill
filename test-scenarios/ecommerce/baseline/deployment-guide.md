# Deployment Guide

## Infrastructure Overview

### Cloud Architecture (AWS-based)

```
Internet
   │
   ▼
CloudFront (CDN)
   │
   ├─── S3 (Static Assets, Product Images)
   │
   ▼
Application Load Balancer
   │
   ├─── Target Group 1 (User Service)
   ├─── Target Group 2 (Product Service)
   ├─── Target Group 3 (Order Service)
   ├─── Target Group 4 (Payment Service)
   └─── Target Group 5 (Cart Service)
         │
         ▼
    ECS/EKS Cluster
    (Docker Containers)
         │
         ▼
    ┌──────────────────────────┐
    │  RDS PostgreSQL          │
    │  (Multi-AZ)              │
    └──────────────────────────┘
         │
         ▼
    ┌──────────────────────────┐
    │  ElastiCache Redis       │
    │  (Cluster Mode)          │
    └──────────────────────────┘
```

## Technology Stack

### Frontend
- **Framework**: React.js 18+ or Next.js 14+
- **State Management**: Redux Toolkit or Zustand
- **Styling**: Tailwind CSS
- **API Client**: Axios or React Query

### Backend
- **Runtime**: Node.js 20+ or Go 1.21+
- **Framework**: Express.js / Fastify (Node) or Gin (Go)
- **API**: RESTful with OpenAPI 3.0 specification
- **Authentication**: JWT with RS256

### Database
- **Primary**: PostgreSQL 15+
- **Cache**: Redis 7+
- **Search**: Elasticsearch 8+ (optional, for advanced search)

### Infrastructure
- **Container Platform**: Docker
- **Orchestration**: Kubernetes (EKS) or ECS with Fargate
- **CI/CD**: GitHub Actions or GitLab CI
- **Monitoring**: Prometheus + Grafana
- **Logging**: ELK Stack (Elasticsearch, Logstash, Kibana)
- **APM**: New Relic or Datadog

## Environment Setup

### Development Environment

```bash
# Prerequisites
- Docker Desktop
- Node.js 20+
- PostgreSQL 15+
- Redis 7+

# Clone repository
git clone <repository-url>
cd ecommerce-platform

# Install dependencies
npm install

# Setup environment variables
cp .env.example .env.local

# Start database
docker-compose up -d postgres redis

# Run migrations
npm run migrate

# Seed database
npm run seed

# Start development server
npm run dev
```

### Environment Variables

```env
# Application
NODE_ENV=development
PORT=3000
API_VERSION=v1

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/ecommerce
DATABASE_POOL_MIN=2
DATABASE_POOL_MAX=10

# Redis
REDIS_URL=redis://localhost:6379
REDIS_TTL=3600

# JWT
JWT_SECRET=your-secret-key
JWT_EXPIRY=24h
JWT_REFRESH_EXPIRY=7d

# Payment Gateway
STRIPE_API_KEY=sk_test_xxxxx
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
PAYPAL_CLIENT_ID=xxxxx
PAYPAL_CLIENT_SECRET=xxxxx

# Email
SENDGRID_API_KEY=xxxxx
EMAIL_FROM=noreply@ecommerce.com

# AWS
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=xxxxx
AWS_SECRET_ACCESS_KEY=xxxxx
S3_BUCKET_NAME=ecommerce-products

# Monitoring
SENTRY_DSN=xxxxx
NEW_RELIC_LICENSE_KEY=xxxxx
```

## Docker Configuration

### Dockerfile (Node.js Service)

```dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

FROM node:20-alpine

WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package*.json ./

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

### docker-compose.yml

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: ecommerce
      POSTGRES_PASSWORD: password
      POSTGRES_DB: ecommerce
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  user-service:
    build:
      context: ./services/user-service
      dockerfile: Dockerfile
    environment:
      - DATABASE_URL=postgresql://ecommerce:password@postgres:5432/ecommerce
      - REDIS_URL=redis://redis:6379
    ports:
      - "3001:3000"
    depends_on:
      - postgres
      - redis

  product-service:
    build:
      context: ./services/product-service
      dockerfile: Dockerfile
    environment:
      - DATABASE_URL=postgresql://ecommerce:password@postgres:5432/ecommerce
      - REDIS_URL=redis://redis:6379
    ports:
      - "3002:3000"
    depends_on:
      - postgres
      - redis

  order-service:
    build:
      context: ./services/order-service
      dockerfile: Dockerfile
    environment:
      - DATABASE_URL=postgresql://ecommerce:password@postgres:5432/ecommerce
      - REDIS_URL=redis://redis:6379
    ports:
      - "3003:3000"
    depends_on:
      - postgres
      - redis

volumes:
  postgres_data:
  redis_data:
```

## Kubernetes Deployment

### Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: ecommerce
```

### Product Service Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
  namespace: ecommerce
spec:
  replicas: 3
  selector:
    matchLabels:
      app: product-service
  template:
    metadata:
      labels:
        app: product-service
    spec:
      containers:
      - name: product-service
        image: ecommerce/product-service:latest
        ports:
        - containerPort: 3000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-secrets
              key: url
        - name: REDIS_URL
          valueFrom:
            secretKeyRef:
              name: redis-secrets
              key: url
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
---
apiVersion: v1
kind: Service
metadata:
  name: product-service
  namespace: ecommerce
spec:
  selector:
    app: product-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: ClusterIP
```

### Ingress Configuration

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ecommerce-ingress
  namespace: ecommerce
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
  - hosts:
    - api.ecommerce.com
    secretName: ecommerce-tls
  rules:
  - host: api.ecommerce.com
    http:
      paths:
      - path: /api/v1/products
        pathType: Prefix
        backend:
          service:
            name: product-service
            port:
              number: 80
      - path: /api/v1/orders
        pathType: Prefix
        backend:
          service:
            name: order-service
            port:
              number: 80
      - path: /api/v1/users
        pathType: Prefix
        backend:
          service:
            name: user-service
            port:
              number: 80
```

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
name: Deploy to Production

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Run linter
        run: npm run lint

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build and push Docker image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          IMAGE_TAG: ${{ github.sha }}
        run: |
          docker build -t $ECR_REGISTRY/product-service:$IMAGE_TAG .
          docker push $ECR_REGISTRY/product-service:$IMAGE_TAG

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Configure kubectl
        uses: azure/k8s-set-context@v3
        with:
          kubeconfig: ${{ secrets.KUBE_CONFIG }}

      - name: Deploy to Kubernetes
        run: |
          kubectl set image deployment/product-service \
            product-service=$ECR_REGISTRY/product-service:${{ github.sha }} \
            -n ecommerce
          kubectl rollout status deployment/product-service -n ecommerce
```

## Database Migration Strategy

### Migration Tool: Flyway or db-migrate

```bash
# Create migration
npm run migrate:create -- add_reviews_table

# Run migrations
npm run migrate:up

# Rollback last migration
npm run migrate:down

# Show migration status
npm run migrate:status
```

### Migration Example

```sql
-- V001__initial_schema.sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

-- V002__add_seller_profiles.sql
CREATE TABLE seller_profiles (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    shop_name VARCHAR(255) NOT NULL
);
```

## Monitoring Setup

### Prometheus Configuration

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'product-service'
    kubernetes_sd_configs:
      - role: pod
        namespaces:
          names:
            - ecommerce
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_label_app]
        regex: product-service
        action: keep
```

### Grafana Dashboard

- Request rate per service
- Error rate
- Response time (p50, p95, p99)
- Database connection pool usage
- Cache hit rate
- Active users

## Security Checklist

- [ ] Enable HTTPS/TLS everywhere
- [ ] Configure CORS properly
- [ ] Implement rate limiting
- [ ] Use parameterized queries (prevent SQL injection)
- [ ] Validate all user inputs
- [ ] Encrypt sensitive data at rest
- [ ] Use secrets management (AWS Secrets Manager)
- [ ] Implement API key rotation
- [ ] Enable database encryption
- [ ] Setup WAF (Web Application Firewall)
- [ ] Regular security audits
- [ ] Dependency vulnerability scanning

## Backup Strategy

### Database Backups
- Automated daily backups (RDS automated backups)
- 7-day retention for point-in-time recovery
- Weekly snapshots retained for 30 days
- Monthly snapshots retained for 1 year

### Disaster Recovery
- Multi-AZ deployment for high availability
- Cross-region replication for critical data
- Regular disaster recovery drills
- RTO (Recovery Time Objective): 4 hours
- RPO (Recovery Point Objective): 1 hour

## Scaling Strategy

### Horizontal Scaling
- Auto-scaling based on CPU/Memory metrics
- Min replicas: 2
- Max replicas: 10
- Target CPU utilization: 70%

### Database Scaling
- Read replicas for read-heavy operations
- Connection pooling (PgBouncer)
- Query optimization and indexing
- Consider sharding for very large datasets

### Caching Strategy
- Cache frequently accessed products
- Cache user sessions
- Cache search results (5-minute TTL)
- Implement cache warming for popular items

## Cost Optimization

- Use spot instances for non-critical workloads
- Right-size instance types based on metrics
- Implement auto-scaling to match demand
- Use reserved instances for baseline capacity
- Optimize database queries to reduce RDS costs
- Implement CloudFront caching to reduce origin requests
- Archive old data to cheaper storage (S3 Glacier)
