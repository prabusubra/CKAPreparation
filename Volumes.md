Volumes:-
  1. static binding
  2. dynamic binding

Static Binding:-
  - inline-mount -> directly configuring volume details with pods.
  - Through Persistent Volume Claims
    steps:
     1. create persistent volumes(PV).
     2. create persistent volume claim(PVC).
     3. binding PVC with PV.
     4. configure PVC with pods.

Without volumes:-

```
apiVersion: v1
kind: Pod
metadata:
  name: p-3
spec:
  containers:
  - name: busybox
    image: busybox
    command:
     - "/bin/sh"
     - "-c"
     - "mkdir -p /usr/temp/prabu; echo 'Welcome to Volumes...' > /usr/temp/prabu/mylog1.log; sleep 1200;"
```
output:-
```
controlplane $ k exec p-3 -- cat /usr/temp/prabu/mylog1.log
Welcome to Volumes...
controlplane $ 
```

HostPath:
 creates the folders on the node in which pod is running.
 ```
apiVersion: v1
kind: Pod
metadata:
  name: pd-2
spec:
  containers:
  - image: busybox
    name: test-container
    command:
    - "/bin/sh"
    - "-c"
    - "echo 'Prabu Subra...' > /usr/temp/prabu/mylog.txt; sleep 3400;"
    volumeMounts:
    - mountPath: /usr/temp/prabu
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /usr/temp/prabu

```
Output:-
```
controlplane $ ssh node01
node01 $ ls /usr/temp/prabu/
mylog.txt

```

EmptyDir:-
create the volume inside pod.

```

apiVersion: v1
kind: Pod
metadata:
  name: p-2
spec:
  containers:
  - name: busybox
    image: busybox
    command:
     - "/bin/sh"
     - "-c"
     - "echo 'Hello Empty dir...' > /usr/temp/prabu/mylog1.log; sleep 3400;"
    volumeMounts:
    - name: temp-storage
      mountPath: /usr/temp/prabu
  volumes:
  - name: temp-storage
    emptyDir: {}
    
```

output:-

 Inside Pod:-
```
controlplane $ kubectl exec -it p-2 -- cat /usr/temp/prabu/mylog1.log
Hello Empty dir...
controlplane $ 
```
 Inside Node:-
 
 ```
 ls -l /var/lib/kubelet/pods/`kubectl get pod p-2 -o 'jsonpath={.metadata.uid}'`/volumes/kubernetes.io~empty-dir
 
 or 
 
 find /  -type d -iname temp-storage 
 
 or
 
 journalctl -u kubelet | grep -i kubeletrootdir
 
 find /var/lib/kubelet -type d -iname empt-storage
 
 
 ```
- Data are deleted afer deleting/restarting pod.
- Data in emptyDir pod is automatically synced with host node.

```
root@controlplane:~# ls /var/lib/kubelet/pods/3b78d8b7-684a-44b5-b5ed-3776b3e284aa/volumes/kubernetes.io~empty-dir/empt-storage

root@controlplane:~# k exec p-3 -- touch /usr/temp/prabu/mylog.log

root@controlplane:~# ls /var/lib/kubelet/pods/3b78d8b7-684a-44b5-b5ed-3776b3e284aa/volumes/kubernetes.io~empty-dir/empt-storage
mylog.log


```
Local Storage:-
 1. create Persistent Volume with local path.
 2. create Persistent Volume Claim.
 3. create Pod with claim.

Creating Persistent Volume:-

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv
spec:
  capacity:
    storage: 50Mi
  volumeMode: Filesystem
  accessModes:
  - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  local:
    path: /usr/local/prabu
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - key: "localvolume"
          operator: "In"
          values:
          - "yes"
```

Creating PVC:-

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 30Mi
```

create label for node.

```

kubectl label node controlplane localvolume=yes

```

create a pod using pvc claim.

```
apiVersion: v1
kind: Pod
metadata:
  name: l-p-2
spec:
  containers:
  - image: busybox
    name: test-container
    command:
    - "/bin/sh"
    - "-c"
    - "echo 'Prabu Subra...' > /usr/temp/prabu/mylog.txt; sleep 1000;"
    volumeMounts:
    - mountPath: /usr/temp/prabu
      name: test-volume
  volumes:
  - name: test-volume
    persistentVolumeClaim:
     claimName: claim1
```
Check file in node.

```
root@controlplane:~# cat /usr/local/prabu/mylog.txt 
Prabu Subra...

```
