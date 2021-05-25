# Taint and toleration

- Tolerations are applied to pods, and allow (but do not require) the pods to schedule onto nodes with matching taints.

- Taints are the opposite. Taints allow a node to repel a set of pods

## Spec: Effects

- *NoSchedule*: If the pod don't match the toleration on the node, It won’t be able to schedule onto the node
- *PreferNoSchedule*: the scheduler will try not to place a Pod that does not tolerate the taint on the node, but it is not required
- *NoExecute*: the scheduler will immediately evict all Pods without the matching toleration from the node

## Use Case

- Dedicated nodes: Users can use a combination of **node affinity and taints/tolerations** to create dedicated nodes
- Nodes with special hardware(GPU): you want to repel Pods that do not need this hardware and attract Pods that do need it
- Taint-based Evictions: cai nay chua hieu lam nen bo qua =))

### Taint node

```sh
kubectl taint node <node-name> key1=value1:NoSchedule
```

### Toleration pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
```
