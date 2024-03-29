NetworkPolicy:-
 controll incomming/outgoing traffice.

Ingress -> incomming traffic.
Egress -> outgoing traffic.

Deny All:-
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-ingress
spec:
  podSelector:
   matchLabels:
    app: front
  policyTypes:
  - Ingress
```

Output:-

```
prabusubra@prabus-MacBook-Pro netpol % k run --rm -it --image=alpine -n beta -- bash
If you don't see a command prompt, try pressing enter.
/ # wget --spider -T 1 10.32.0.2:80
Connecting to 10.32.0.2:80 (10.32.0.2:80)
remote file exists
/ #
```
Namespace level traffice controll:-

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-ingress
spec:
  podSelector:
   matchLabels:
    app: front
  ingress:
  - from:
    - namespaceSelector:
       matchLabels:
        app: beta
```

```
prabusubra@prabus-MacBook-Pro netpol % k run --rm -it --image=alpine -n beta -- bash
If you don't see a command prompt, try pressing enter.
/ # wget --spider -T 1 10.32.0.2 80
Connecting to 10.32.0.2 (10.32.0.2:80)
remote file exists
```

```
prabusubra@prabus-MacBook-Pro netpol % k run --rm -it --image=alpine -n gamma -- bash
If you don't see a command prompt, try pressing enter.
/ # wget --spider -T 1 10.32.0.2 80
Connecting to 10.32.0.2 (10.32.0.2:80)
wget: download timed out
```
Pod level traffice controll:-

```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-pod-ingress
spec:
  podSelector:
   matchLabels:
    app: front
  ingress:
  - from:
    - podSelector:
       matchLabels:
        app: mid
```

Output:-
```
rabusubra@prabus-MacBook-Pro netpol % k run --rm -it --image=alpine --labels=app=mid -- bash
If you don't see a command prompt, try pressing enter.
/ # wget --spider -T 1 10.32.0.2:80
Connecting to 10.32.0.2:80 (10.32.0.2:80)
remote file exists
```

```
prabusubra@prabus-MacBook-Pro netpol % k run --rm -it --image=alpine --labels=app=db -- bash
If you don't see a command prompt, try pressing enter.
/ # wget --spider -T 1 10.32.0.2:80
Connecting to 10.32.0.2:80 (10.32.0.2:80)
wget: download timed out
```
```
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: port-policy-2
spec:
  podSelector:
    matchLabels:
      app: front
  ingress:
    - from:
        - podSelector: {}
      ports:
        - port: 80
       
 ---

apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: port-policy-1
spec:
  podSelector:
    matchLabels:
      app: front
  ingress:
    - from:
        - podSelector:
           matchLabels:
            app: back
      ports:
        - port: 80
       
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: front
  name: ng-1
spec:
  containers:
  - image: nginx
    name: ng-1
    ports:
    - containerPort: 80

---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    run: ng-1
  name: ng-1
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: front
```

```
k run bb1 -it --rm --image=busybox --labels=app=back -- wget --spider --timeout=2 10.244.0.11:80
```
