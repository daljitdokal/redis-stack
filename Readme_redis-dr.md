# Redis DR setup
Redis primary cluster on one OpenShift cluster and set up a DR replica on a separate OpenShift cluster to enable cross-cluster failover and failback. This setup adds robustness for disaster recovery by ensuring Redis has a backup site in case the primary OpenShift cluster fails. Here’s how to configure it:

## Steps for Cross-Cluster Redis Primary-Replica Deployment in OpenShift

### Set Up the Primary Redis Cluster on Cluster A

- Deploy Redis on the primary OpenShift cluster (Cluster A) as the main primary node using a Helm chart or OpenShift template.
- Use persistent storage (e.g., Ceph, GlusterFS, EBS) with multi-zone replication if possible.
- Expose Redis through a service with a ClusterIP type for internal use, and configure an external load balancer if you need cross-cluster communication.

### Set Up the Replica Redis Cluster on Cluster B

- On the DR OpenShift cluster (Cluster B), deploy Redis in replica mode.
- Configure persistent storage here as well to ensure data is durable even if pods restart.
- Configure the Redis instance in Cluster B to recognize Cluster A’s primary node as its replication source.

### Networking Configuration for Cross-Cluster Communication

- Service Exposure: Use OpenShift’s Route feature or an external load balancer to expose Redis on Cluster A’s primary node. This allows the replica node in Cluster B to communicate with the primary node.
- Firewall and VPN: Ensure that both clusters have secure network connectivity. This might include setting up a VPN or using a secure tunnel if the clusters are in separate private networks.
- DNS and Service Discovery: Use a static IP or DNS to address the primary Redis node in Cluster A, allowing the replica in Cluster B to consistently reach it.

### Configure Redis Replication

- In Cluster B’s replica configuration, point to the Redis primary instance in Cluster A by setting the replicaof directive in the Redis configuration file or through environment variables.
- Example configuration for Cluster B’s Redis to replicate from Cluster A:

```yaml
  Copy code
  replica:
    enabled: true
    replicaof: "<Cluster_A_Redis_IP> <port>"
```

### Automate Failover and Failback with Redis Sentinel or Custom Scripting

- Redis Sentinel: Sentinel can help with monitoring, automatic failover, and promotion of replicas, but it typically requires being in the same network for automated handling.
- Manual Failover and Failback: In many cross-cluster setups, manual failover is preferred. You can use scripts or a control plane to manage promotion of Cluster B’s replica to primary if Cluster A goes down.
- Failover Process: In a failure, promote the Cluster B replica to become the new primary by disabling replication and reconfiguring clients to connect to the new primary in Cluster B.
- Failback Process: Once Cluster A is operational again, restore it as a replica of the new primary in Cluster B or reestablish Cluster A as the primary, with Cluster B replicating from it.

### Testing Failover and Failback

- Perform controlled failover tests to ensure that both clusters can handle role changes smoothly.
- Verify that data integrity is maintained throughout the failover and failback processes.

### Monitoring and Alerts

- Use Prometheus and Grafana for monitoring Redis metrics across both clusters.
- Set up alerting for replication lag, node availability, and other key metrics to promptly respond to any failures.

### Summary Workflow for Failover and Failback

- Normal Operation: Cluster A acts as the primary, and Cluster B replicates data from it.
- Failover: When Cluster A fails, manually or automatically promote Cluster B as the primary.
- Failback: After restoring Cluster A, reconfigure it as a replica of Cluster B, or reestablish it as the primary with Cluster B as the replica.

This approach ensures high availability and disaster recovery across OpenShift clusters.
