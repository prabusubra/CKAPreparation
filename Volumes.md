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
