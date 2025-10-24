# Step 1: Fix Deployment Component
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 4
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: busybox-pod
    spec:
      containers:
        - command:
            - sh
            - -c
            - echo Hello Kubernetes! && sleep 3600
          image: busybox888
          imagePullPolicy: Always
          name: busybox-container

kubectl set image deployment frontend-deployment busybox-container=busybox

kubectl get deploy frontend-deployment -o yaml | yq 'del(.metadata.annotations, .status)'

# Step 2: Deployment Kind Error
apiVersion: apps/v1
kind: deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600

# Step 3: Imperative Deployment Creation
kubectl create deployment web-demo --image=nginx --replicas=2
kubectl rollout history deployment web-demo

# Step 4: Update Strategy Demo
kubectl set image deployment/web-demo nginx=nginx:1.19.2
kubectl set image deployment/web-demo nginx=nginx:1.21 --record
kubectl rollout status deployment/web-demo
kubectl rollout history deployment/web-demo

# Step 5: Rollback Demo
kubectl rollout undo deployment/web-demo
kubectl annotate deployment web-demo kubernetes.io/change-cause="image updated to nginx latest"

# Step 6: Deployment Scale Replicas
kubectl scale deployment/web-demo --replicas=1
