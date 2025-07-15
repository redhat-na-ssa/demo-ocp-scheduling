# Notes

## API Priority & Fairness (APF)

See `FlowSchema` and `PriorityLevelConfiguration`

```yaml
apiVersion: flowcontrol.apiserver.k8s.io/v1alpha1
kind: FlowSchema
metadata:
  name: restrict-pod-lister
spec:
  priorityLevelConfiguration:
    name: restrict-pod-lister
  distinguisherMethod:
    type: ByUser
  rules:
  - resourceRules:
    - apiGroups: [""]
      namespaces: ["demo"]
      resources: ["pods"]
      verbs: ["list", "get"]
    subjects:
    - kind: ServiceAccount
      serviceAccount:
        name: podlister-0
        namespace: demo
    - kind: ServiceAccount
      serviceAccount:
        name: podlister-1
        namespace: demo 
    - kind: ServiceAccount
      serviceAccount:
        name: podlister-2
        namespace: demo            
---
apiVersion: flowcontrol.apiserver.k8s.io/v1alpha1
kind: PriorityLevelConfiguration
metadata:
  name: restrict-pod-lister
spec:
  type: Limited
  limited:
    assuredConcurrencyShares: 5
    limitResponse:
      queuing:   
        queues: 10
        queueLengthLimit: 20
        handSize: 4
      type: Queue
```

- https://www.redhat.com/en/blog/surviving-the-api-storm-with-api-priority-fairness

## Quality of Service for Pods

Types of classes:

Guaranteed

```yaml
kind: Pod
apiVersion: v1
spec:
  containers:
    - resources:
        limits:
          cpu: '1'
          memory: 2400Mi
status:
  qosClass: Guaranteed
```

Burstable

```yaml
kind: Pod
apiVersion: v1
spec:
  containers:
    - resources:
        requests:
          cpu: '1'
          memory: 2400Mi
status:
  qosClass: Burstable
```

BestEffort

```yaml
kind: Pod
apiVersion: v1
spec:
  containers:
    - resources: {}

status:
  qosClass: BestEffort
```

- https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod