# mzr-deployment

To Attach a volume to machine use the following URL (https://cloud.ibm.com/docs/containers?topic=containers-utilities#vpc_api_attach)

- Attach block volume to the machine 
- ssh to the machine and mount the volume
- you can ssh via privilged pod you can you the below deployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        resources: {}
        securityContext:
          privileged: true
        volumeMounts:
        - mountPath: /host
          name: host-storage
      dnsPolicy: ClusterFirst
      hostIPC: true
      hostNetwork: true
      hostPID: true
      volumes:
      - hostPath:
          path: /
        name: host-storage
```


the previous pod is privilged pod that can be used to access the underlying worker nodes as privilged pod using the following commands

```
kubectl exec -ti <podname> bash
chroot /host
bash
```


After logging into worker nodes run block volume is defind as /dev/vdd run the following command to add to mound it 


```
   parted /dev/vdd mklabel gpt
   parted -a opt /dev/vdd mkpart primary ext4 0% 100%
   mkfs.ext4 -L data /dev/vdd1
   mkdir -p /mnt/data
   echo 'LABEL=data /mnt/data ext4 defaults 0 2' >> /etc/fstab
   mount /mnt/data

```

