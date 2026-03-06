## PROJECT: Define and Deploy an Application in ArgoCD
### STEP 1: Confirm ArgoCD is Running

First, verify ArgoCD is installed and running:

```
kubectl get pods -n argocd
```

### STEP 2: Prepare Your Git Repository

ArgoCD pulls applications from Git. 

**deployment.yaml**

```

apiVersion: apps/v1
kind: Deployment
metadata:
  name: guestbook
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      app: guestbook
  template:
    metadata:
      labels:
        app: guestbook
    spec:
      containers:
        - name: guestbook
          image: sirwills/guessbook:latest
          ports:
            - containerPort: 80
```

**service.yaml**

```

apiVersion: v1
kind: Service
metadata:
  name: guestbook
  namespace: default
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30007
  selector:
    app: guestbook
```

STEP 3: Create the ArgoCD Application YAML

Now create:
 ```
 my-app.yaml
 ```

 ```
 apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-argocd-app
  namespace: argocd
spec:
  project: default

  source:
    repoURL: https://github.com/Nonsocha/Gitops-App-Dep.git
    targetRevision: HEAD
    path: .

  destination:
    server: https://kubernetes.default.svc
    namespace: default

  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### STEP 4: Apply the Application

Now deploy it:

```
 my-app.yaml
 ```
 Verify:

 ```
 kubectl get applications -n argocd
```

### STEP 5: Access ArgoCD UI

If using port-forward:
```
kubectl port-forward svc/argocd-server -n argocd 9090:443
```
Then open

```
https://localhost:9090
```

### STEP 6: Sync the Application

Inside ArgoCD UI:

- Click your app

- Click SYNC

- Confirm

### STEP 7: Verify Deployment

 ```
 kubectl get pods
kubectl get svc
```
Then access

```
http://<EC2-PUBLIC-IP>:30007
```

Create a Dockerfile:

 Dockerfile:

 ```
  From ngnix:latest

  copy .../2137_barista_cafe/* /usr/share/ngnix/html
```
  Build the image:

  ```
  docker build -t yourdockerhubusername/guessbook
   .
```

push:
```
docker push yourdockerhubusername/guessbook
```

### 7 Deploye with AgroCD

On your EC2 Kubernetes cluster run:

```
kubectl apply -f argocd/app-definition.yaml
```

Verify:

```
kubectl get applications -n argocd
```

Check Pods

```
kubectl get pods
```

### Step 8 Access the Website

```
http://EC2-PUBLIC-IP:30009
```

