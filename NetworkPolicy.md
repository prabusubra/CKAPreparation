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
