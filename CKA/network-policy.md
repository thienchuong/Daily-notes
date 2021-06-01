# Network Policy

## Deny all traffic to an application

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-traffic-to-app
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: db  #deny all trafic to pod with label app=db
  policyTypes:
  - Ingress     #deny
```

## Limit traffic to an application

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: limit-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: db   # pod with label app=db only accept traffic from pod with label role=backend
  policyTypes:
  - Ingress
  ingress:
    - podSelector:
        matchLabels:
          app: backend 
```

## Allow all traffic to an application

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-to-app
spec:
  podSelector:
    matchLabels:
      app: db
  policyTypes:
  - Ingress
  ingress:      # allow all
  - {}          # allow all
```

## Deny all traffic to a default namespace

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
```

> `podSelector` is empty which mean sellect all pods

> `ingress` rules not specified -> block all incoming traffic

## Deny all traffic from other namespaces

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all-from-other-ns
  namespace: default
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector: {}
```

## Allow traffic to an application from all namespaces

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-to-app-from-all-ns
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector: {}
```

## Allow all traffic from a namespace

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          project: myproject
```

## Allow all traffic from some pods in another namespace

```yml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          user: alice
      podSelector:
        matchLabels:
          role: client
```

>  only pods with label `role=client` in namespaces labelled `user: alice` can access pod with label `role=db`

## Allow traffic only to a specific port of an application

```yml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: api-allow-port5000
spec:
  podSelector:
    matchLabels:
      app: apiserver
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 5000
```

> Allow traffic on port 5000 from pods with label `role=frontend` in the same namespace.