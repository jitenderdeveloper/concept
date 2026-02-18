
# Scalable Cloud Architecture

```mermaid
flowchart LR
Users --> CDN
CDN --> LB[Load Balancer]
LB --> App1
LB --> App2
App1 --> DB[(DB Cluster)]
App2 --> DB
App1 --> Cache[Redis]
App2 --> Cache
```
