# ETCD backup

## Doc

[ETCD](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster)

## Set etcd version

```sh
export ETCDCTL_API=3
```

## Backup etcd

```sh
etcdctl --endpoints=https://127.0.0.1:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
--key=/etc/kubernetes/pki/etcd/healthcheck-client.key snapshot save /tmp/etcd-backup.db
```

## Restore ETCD Snapshot to the give path by the Exam

```sh
etcdctl --endpoints=https://127.0.0.1:2379 \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/healthcheck-client.crt \
--key=/etc/kubernetes/pki/etcd/healthcheck-client.key \
--snapshot restore /tmp/etcd-backup.db \
--data-dir /give/path/by/the/Exam

#or

ETCDCTL_API=3 etcdctl  --data-dir /give/path/by/the/Exam snapshot restore /opt/snapshot-pre-boot.db
```

## Modify /etc/kubernetes/manifests/etcd.yaml

```yaml
vim /etc/kubernetes/manifests/etcd.yaml

volumes:
  - hostPath:
      path: /give/path/by/the/Exam
      type: DirectoryOrCreate
    name: etcd-data
```

## ETCD exam tip

In the CKA exam, your ETCD cluster will run on local node (the worker node you use to interact with another K8S clusters)

- Step 1: take backup
- Step 2: restore to the path given by the Exam
- Step 3: verify ETCD cluster `systemctl status etcd`
- Step 4: `cat /etc/systemd/system/etcd.service`

```bash
[Unit]
Description=etcd
Documentation=https://github.com/coreos

[Service]
ExecStart=/usr/bin/etcd
–name master-1
–cert-file=/etc/etcd/etcd.crt
–key-file=/etc/etcd/etcd.key
–peer-cert-file=/etc/etcd/etcd.crt
–peer-key-file=/etc/etcd/etcd.key
–trusted-ca-file=/etc/etcd/ca.crt
–peer-trusted-ca-file=/etc/etcd/ca.crt
–peer-client-cert-auth
–client-cert-auth
–initial-advertise-peer-urls https://xxxxx:2380
–listen-peer-urls https://xxxxx:2380
–listen-client-urls https://xxxxx:2379,https://127.0.0.1:2379
–advertise-client-urls https://xxxxx:2379
–initial-cluster-token etcd-cluster-0
–initial-cluster master-1=https://xxxxx:2380
–initial-cluster-state new
–data-dir=/var/lib/etcd -> change to the path given by the Exam /give/path/by/the/Exam
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
```

- Step 3: `systemctl retsart etcd`
