# PriorityClass Demo

## Quick Start

<!-- ```sh
oc apply -k demo
``` -->

## Notes

Before you set up PriorityClasses, there are a few things to consider.

- The pods without a `priorityClassName` will be treated as priority `0`.
- Use a consistent naming convention for all PriorityClasses.
- Make sure that the pods for your workloads are running with the right `PriorityClass`.

```sh
oc get priorityclass
oc describe priorityclass
```

OpenShift Default `priorityClasses`

```yaml
---
apiVersion: scheduling.k8s.io/v1
description: This priority class should be used for user facing OpenShift workload
  pods only.
kind: PriorityClass
metadata:
  name: openshift-user-critical
preemptionPolicy: PreemptLowerPriority
value: 1000000000
---
apiVersion: scheduling.k8s.io/v1
description: Used for system critical pods that must run in the cluster, but can
  be moved to another node if necessary.
kind: PriorityClass
metadata:
  name: system-cluster-critical
preemptionPolicy: PreemptLowerPriority
value: 2000000000
---
apiVersion: scheduling.k8s.io/v1
description: Used for system critical pods that must not be moved from their current
  node.
kind: PriorityClass
metadata:
  name: system-node-critical
preemptionPolicy: PreemptLowerPriority
value: 2000001000
```

## Links

- https://kubernetes.io/blog/2023/01/12/protect-mission-critical-pods-priorityclass
- https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler
- https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption
- [Scheduling Policies (pre k8s v1.23)](https://kubernetes.io/docs/reference/scheduling/policies)
- https://kubernetes.io/docs/reference/scheduling/config
- https://docs.openshift.com/container-platform/4.11/nodes/scheduling/nodes-scheduler-about.html
- https://docs.openshift.com/container-platform/4.11/nodes/scheduling/nodes-descheduler.html
- https://docs.openshift.com/container-platform/4.11/nodes/scheduling/nodes-scheduler-profiles.html
- [Namespace Node Selection](https://docs.openshift.com/container-platform/4.11/nodes/scheduling/nodes-scheduler-node-selectors.html#nodes-scheduler-node-selectors-project_nodes-scheduler-node-selectors)
