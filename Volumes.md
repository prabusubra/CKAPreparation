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

```
controlplane $ kubectl exec -it p-2 -- cat /usr/temp/prabu/mylog1.log
Hello Empty dir...
controlplane $ 
```
