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
