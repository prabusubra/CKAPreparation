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
    - mountPath: /usr/temp
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /usr/temp

```

controlplane $ ssh node01
node01 $ ls /usr/temp/prabu/
mylog.txt
node01 $ 
