# ConfigMap

## What is ConfigMap

- Useful for storing and sharing non-sensitive, unencrypted configuration information
- API object used to store non-confidential data in key-value pairs
- keep your application code separate from your configuration
- This makes their configurations easier to change and manage, and prevents hardcoding configuration data to Pod specifications.

## Immutable ConfigMaps

- Protects from accidental (or unwanted) updates that could cause applications outages
- Improves performance of your cluster by significantly reducing load on kube-apiserver, by closing watches for ConfigMaps marked as immutable
- Controlled by the ImmutableEphemeralVolumes

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  ...
data:
  ...
immutable: true
```

- Once a ConfigMap is marked as immutable, it is not possible to revert this change -> can only delete and recreate the ConfigMap. Because existing Pods maintain a mount point to the deleted ConfigMap, it is recommended to recreate these pods

### Mount a ConfigMap as a Volume to use in Pod

- Define the ConfigMap

```yaml
kind: ConfigMap 
apiVersion: v1 
metadata:
  name: example-configmap 
data:
  # Configuration values can be set as key-value properties
  database: mongodb
  database_uri: mongodb://localhost:27017
  
  # Or set as complete file contents (even JSON!)
  keys: | 
    image.public.key=771 
    rsa.public.key=42
```

- Mount the ConfigMap through a Volume

```yaml
kind: Pod 
apiVersion: v1 
metadata:
  name: pod-using-configmap 

spec:
  volumes:
    # name must match the name specified in the volume mount
    - name: example-configmap-volume
      # Populate the volume with config map data
      configMap:
        # name here must match the name specified in the ConfigMap's YAML 
        name: example-configmap

  containers:
    - name: container-configmap
      image: nginx:1.7.9
      # Mount the volume that contains the configuration data into your container filesystem
      volumeMounts:
        # name must match the name from the volumes section of this pod
        - name: example-configmap-volume
            mountPath: /etc/config
```

### Use ConfigMap with Environment Variables and `envFrom`

```yaml
kind: Pod 
apiVersion: v1 
metadata:
  name: pod-env-var 
spec:
  containers:
    - name: env-var-configmap
      image: nginx:1.7.9 
      envFrom:
        - configMapRef:
            name: example-configmap
```

### The other ways to create and use ConfigMaps

- Use the contents of an entire directory 
  
```sh
  kubectl create configmap my-config --from-file=./my/dir/path/
```

- Use the contents of a file or specific set of files

```sh
  kubectl create configmap my-config --from-file=./my/file.txt
```

- Use literal key-value pairs defined on the command line 

```sh
kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2
```
