There are two different ways to deploy a Redis Cluster, using the [Redis&reg; Helm Chart](https://github.com/bitnami/charts/tree/main/bitnami/redis) or the [Redis&reg; Cluster Helm Chart](https://github.com/bitnami/charts/tree/main/bitnami/redis-cluster).

### Choose between Redis&reg; Helm Chart and Redis&reg; Cluster Helm Chart

You can choose any of the two Redis&reg; Helm charts for deploying a Redis&reg; cluster.
While [Redis&reg; Helm Chart](https://github.com/bitnami/charts/tree/main/bitnami/redis) will deploy a master-slave cluster using Redis&reg; Sentinel, the [Redis&reg; Cluster Helm Chart](https://github.com/bitnami/charts/tree/main/bitnami/redis-cluster) will deploy a Redis&reg; Cluster with sharding.
The main features of each chart are the following:

| Redis&reg;                                             | Redis&reg; Cluster                                             |
|--------------------------------------------------------|------------------------------------------------------------------------|
| Supports multiple databases                            | Supports only one database. Better if you have a big dataset           |
| Single write point (single master)                     | Multiple write points (multiple masters)                               |
| ![Redis&reg; Topology](img/redis-topology.png) | ![Redis&reg; Cluster Topology](img/redis-cluster-topology.png) |

Both solutions provide a simply and reliable way to run Redis in a production enevironment. Keep reading to discover the differences between them and check which one better suits your needs.

The Redis Cluster Helm chart configures a cluster with six nodes by default with multiple writing points (three masters) and three slave nodes. The Redis Helm chart deploys three nodes by default in which there is only one writing point (one master) and two nodes for replicas (slaves).
The Redis Cluster Helm chart will deploy a Redis Cluster topology with sharding while the Redis Cluster will deploy a master-slave cluster using Redis Sentinel.
The Redis Cluster supports only one database - indicated if you have a big dataset - and Redis supports multiple databases.
The Redis Cluster client must support redirection, while the client used for Redis doesn't need it.
The topology of the Redis Cluster Helm chart allows users to access the cluster both externally and internally and you can both scale up and scale down the cluster in both accesses.
The Redis Cluster Helm chart also includes an additional feature: disaster recovery and failover. If the master node or even all the nodes are down, the cluster is automatically recovered and new master nodes are promoted to maintain the balance of the cluster and ensure that read/write operations continue without interruption.
A diagram comparing the Redis Helm chart versus the Redis Cluster Helm chart topologies