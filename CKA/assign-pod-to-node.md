# Assign pod to node

## Node selector

- label node

```bash
kubectl label nodes <node-name> <key>=<value>
```

- Add a nodeSelector field to your pod configuration

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
  nodeSelector:
    key: value
```

## Node Affinity

- *requiredDuringSchedulingIgnoredDuringExecution* specifies rules that must be met for a pod to be scheduled onto a node 

- *preferredDuringSchedulingIgnoredDuringExecution* specifies that the scheduler will try to enforce but will not guarantee.

- *requiredDuringSchedulingRequiredDuringExecution*: evict pods from nodes that cease to satisfy the pods' node affinity requirements

- *IgnoredDuringExecution*: if labels on a node change at runtime such that the affinity rules on a pod are no longer met, the pod continues to run on the node

## Inter-Pod Affinity Anti-Affinity

- Define whether a given Pod should or should not be scheduled onto a particular node based on labels of other Pods already running on that node

- Affinity rules is to schedule related pods to be close to each other for performance reasons:

  - Co-locate the Pods from a particular service or Job in the same AZ.
  - Co-locate the Pods from two services dependent on each other on one node to reduce network latency between them
  - Co-location -> same nodes and/or same availability zone.

- Anti-affinity rules is to schedule related pods not too close to each other for high availability reasons.
  - Spread the Pods of a service across nodes and/or availability zones to reduce correlated failures. For example, we may want to prevent data Pods of some database (e.g., Elasticsearch) to live on the same node to avoid the single point of failure
  - Give a Pod “exclusive” access to a node to guarantee resource isolation

The Pod affinity/anti-affinity may be formalized as follows: `this Pod should or should not run in an X node if that X node is already running one or more pods that meet rule Y`

`Y` is a `LabelSelector` of Pods running on that node. `X` may be a node, rack, CSP region, CSP zone, etc. Users can express it using a `topologyKey`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: failure-domain.beta.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: kubernetes.io/hostname
  containers:
  - name: with-pod-affinity
    image: k8s.gcr.io/pause:2.0
```

## Node name

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  nodeName: kube-01
```
