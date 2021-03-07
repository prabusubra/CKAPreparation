1. Display all the pods sorted by start time

Ans:- k get pods --sort-by=status.startTime

2. Create a job that runs every 3 minutes and prints out the current time.

Ans:-
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/3 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date;
          restartPolicy: OnFailure

3. Create a job that runs 20 times, 5 containers at a time, and prints “Hello parallel world”

Ans:-
apiVersion: batch/v1
kind: Job
metadata:
  name: job1
spec:
  completions: 20
  parallelism: 5
  template:
    spec:
      containers:
      - name: job-container
        image: busybox
        command:
         - /bin/sh
         - -c
         - echo Hello parallel world
      restartPolicy: Never
  backoffLimit: 4

4. Create a horizontal autoscaling group that starts with 2 pods and scales when CPU usage is over 50%

Ans:-
k create deploy auto-1 --image=nginx
k autoscale deploy auto-1 --cpu-percent=50 --min=2 --max=10
k get hpa

5. Create a networking policy such that only pods with the label access=granted can talk to it.

    Create a nginx pod and attach this policy to it.
    Create a busybox pod and attempt to talk to nginx - should be blocked
    Attach the label to busybox and try again - should be allowed
    
    apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  ingress:
  - from:  
    - podSelector:
       matchLabels:
        access: granted

6. Create a namespace

    Run a pod in the new namespace
    Put memory limits on the namespace
    Limit pods to 2 persistent volumes in this namespace
    
    Ans:-
    
    k create namespace alpha
    k run ng-alpha --image=nginx --namespace=alpha
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: alpha-quota
    spec:
      hard:
        memory: 200Gi
      persistentvolumeclaims: "2"
    
    k create -f rq-1.yaml -n alpha
    



