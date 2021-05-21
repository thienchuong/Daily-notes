# Service

```yaml
kind: Service
apiVersion: v1
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - name: http
      nodePort: 30475   # Node's port
      port: 8089        # Service's port
      protocol: TCP
      targetPort: 8080  # Containter's port
```

let say your host machine is `10.10.10.20` you can hit service http by `10.10.10.20:30745` :

```
flow
host port -> service port -> container port
```