# Scalable Cloud Architecture — MONITORING Platform

## Cloud Strategy

| Property       | Value                                          |
| -------------- | ---------------------------------------------- |
| Primary Cloud  | AWS / GCP                                      |
| Multi-region   | Yes (Active-Active or Active-Passive)          |
| CDN            | Cloudflare / AWS CloudFront                    |
| Load Balancing | AWS ALB / GCP Load Balancer                    |
| Auto Scaling   | Kubernetes HPA + Cluster Autoscaler            |
| DB Scaling     | Read Replicas + Connection Pooling (PgBouncer) |
| Cache          | Redis Cluster (ElastiCache / Memorystore)      |

---

## Full Cloud Architecture Diagram

```mermaid
flowchart LR
    subgraph Users
        MOBILE[Mobile Apps]
        WEB[Web Admin Panel]
    end

    subgraph EdgeLayer[Edge Layer]
        CF[Cloudflare CDN + DDoS]
        WAF[WAF]
    end

    subgraph Region1[AWS Region 1 - Primary]
        ALB1[Application Load Balancer]

        subgraph K8S1[Kubernetes Cluster]
            APIGW1[API Gateway]
            SERVICES1[Microservices]
        end

        subgraph Data1[Data Layer]
            PG_PRIMARY[(PostgreSQL Primary)]
            REDIS1[(Redis Cluster)]
            KAFKA1[Kafka Cluster]
        end

        S3_1[S3 Storage]
    end

    subgraph Region2[AWS Region 2 - DR / Scale]
        ALB2[Application Load Balancer]

        subgraph K8S2[Kubernetes Cluster]
            APIGW2[API Gateway]
            SERVICES2[Microservices]
        end

        subgraph Data2[Data Layer]
            PG_REPLICA[(PostgreSQL Read Replica)]
            REDIS2[(Redis Cluster)]
        end

        S3_2[S3 Storage - Replica]
    end

    subgraph Monitoring[Monitoring & Observability]
        PROM[Prometheus]
        GRAFANA[Grafana]
        ELK[ELK Stack]
        ALERT[Alert Manager]
    end

    MOBILE --> CF --> WAF
    WEB --> CF
    WAF --> ALB1
    WAF --> ALB2

    ALB1 --> K8S1
    K8S1 --> Data1
    K8S1 --> S3_1

    ALB2 --> K8S2
    K8S2 --> Data2
    K8S2 --> S3_2

    PG_PRIMARY -->|Replication| PG_REPLICA
    S3_1 -->|Replication| S3_2

    K8S1 --> PROM
    K8S2 --> PROM
    PROM --> GRAFANA
    K8S1 --> ELK
    PROM --> ALERT
```

---

## Auto Scaling Strategy

```mermaid
flowchart LR
    TRAFFIC[Increased Traffic] --> HPA[Horizontal Pod Autoscaler]
    HPA -->|CPU > 70%| SCALE_PODS[Scale Up Pods]
    HPA -->|CPU < 30%| SCALE_DOWN[Scale Down Pods]

    SCALE_PODS --> CLUSTER_CHECK{Enough Nodes?}
    CLUSTER_CHECK -->|No| CA[Cluster Autoscaler]
    CA --> NEW_NODE[Provision New Node]
    NEW_NODE --> SCALE_PODS

    CLUSTER_CHECK -->|Yes| DEPLOY_POD[Deploy New Pod]
```

---

## Database Scaling

```mermaid
flowchart LR
    APP[Application] --> PGBOUNCER[PgBouncer - Connection Pool]
    PGBOUNCER --> PG_PRIMARY[(PostgreSQL Primary)]
    PG_PRIMARY -->|Streaming Replication| PG_READ1[(Read Replica 1)]
    PG_PRIMARY -->|Streaming Replication| PG_READ2[(Read Replica 2)]

    APP -->|Read Queries| PG_READ1
    APP -->|Read Queries| PG_READ2
    APP -->|Write Queries| PG_PRIMARY

    APP --> REDIS[(Redis Cache)]
    REDIS -->|Cache Hit| APP
    REDIS -->|Cache Miss| PG_PRIMARY
```

---

## Cost Estimation by Scale

| Scale                | Compute | Database | Storage | Realtime/Push | Total/Month |
| -------------------- | ------- | -------- | ------- | ------------- | ----------- |
| MVP (1 college)      | $80     | $40      | $10     | $20           | ~$150       |
| Growth (10 colleges) | $300    | $150     | $50     | $80           | ~$580       |
| Scale (100 colleges) | $800    | $400     | $200    | $300          | ~$1,700     |
| Enterprise (500+)    | $2,000  | $1,000   | $500    | $800          | ~$4,300     |
