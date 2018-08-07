---
layout: layout.pug
navigationTitle: Prometheus Federation
title: Federation
menuWeight: 35
excerpt: Configurating Federation for Prometheus
featureMaturity:
enterprise: false
---

# NodeSync:
NodeSync is an easy to use continuous background repair that has low overhead and provides consistent performance.

 -  Continuously validates that data is in sync on all replica.
 -  Always running but low impact on cluster performance
 -  Fully automatic, no manual intervention needed
 -  Completely replace anti-entropy repairs


## Starting and Stopping the NodeSync service

 The NodeSync service automatically starts with the dse cassandra command. You can manually start and stop the service on each node.
 
 **Procedure:**

 Verify the status of the NodeSync service:

```
    nodetool nodesyncservice status
```

  The output should indicate running.
  
```
    The NodeSync service is running
```

### Disable the NodeSync service:

```
nodetool nodesyncservice disable
```

 On the next restart of DataStax Enterprise service (DSE), the NodeSync service will start up.


Verify the status of the NodeSync service:

```
nodetool nodesyncservice status
```

The output should indicate not running.

```
The NodeSync service is not running
```
