# What is static pods

- Static pods are pods created and managed by kubelet daemon on a specific node without API server observing them. 

- If the static pod crashed, kubelet restarts them. Control plane is not involved in lifecycle of static pod. 

## Use case

- Static pods are useful in cluster bootstrapping cases and may be deprecated in the future.