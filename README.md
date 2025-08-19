# 1️⃣ Go to home directory (or where you want the project)
cd ~

# 2️⃣ Create project folder
mkdir -p devops-ecommerce-app/k8s/base
cd devops-ecommerce-app

# 3️⃣ Initialize git
git init
git branch -M main

# 4️⃣ Add GitHub remote
git remote add origin https://github.com/amenvi18-tech/devops-ecommerce-app.git

# 5️⃣ Create frontend.yaml
cat > k8s/base/frontend.yaml <<EOL
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: docker.io/amenvi18-tech/devops-ecommerce-frontend:latest
        ports:
        - containerPort: 80
        env:
        - name: API_CATALOG_URL
          value: "http://catalog:5000"
        - name: API_ORDERS_URL
          value: "http://orders:5001"
---
apiVersion: v1
kind: Service
metadata:
  name: frontend
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
EOL

# 6️⃣ Create catalog.yaml
cat > k8s/base/catalog.yaml <<EOL
apiVersion: apps/v1
kind: Deployment
metadata:
  name: catalog
  labels:
    app: catalog
spec:
  replicas: 2
  selector:
    matchLabels:
      app: catalog
  template:
    metadata:
      labels:
        app: catalog
    spec:
      containers:
      - name: catalog
        image: docker.io/amenvi18-tech/devops-ecommerce-catalog:latest
        ports:
        - containerPort: 5000
        env:
        - name: DB_HOST
          value: catalog-db
        - name: DB_PORT
          value: "3306"
        - name: DB_USER
          value: cataloguser
        - name: DB_PASSWORD
          value: catalogpass123
        - name: DB_NAME
          value: catalogdb
---
apiVersion: v1
kind: Service
metadata:
  name: catalog
spec:
  type: ClusterIP
  selector:
    app: catalog
  ports:
  - port: 5000
    targetPort: 5000
EOL

# 7️⃣ Create orders.yaml
cat > k8s/base/orders.yaml <<EOL
apiVersion: apps/v1
kind: Deployment
metadata:
  name: orders
  labels:
    app: orders
spec:
  replicas: 2
  selector:
    matchLabels:
      app: orders
  template:
    metadata:
      labels:
        app: orders
    spec:
      containers:
      - name: orders
        image: docker.io/amenvi18-tech/devops-ecommerce-orders:latest
        ports:
        - containerPort: 5001
        env:
        - name: DB_HOST
          value: orders-db
        - name: DB_PORT
          value: "3306"
        - name: DB_USER
          value: ordersuser
        - name: DB_PASSWORD
          value: orderspass123
        - name: DB_NAME
          value: ordersdb
---
apiVersion: v1
kind: Service
metadata:
  name: orders
spec:
  type: ClusterIP
  selector:
    app: orders
  ports:
  - port: 5001
    targetPort: 5001
EOL

# 8️⃣ Stage and commit all files
git add .
git commit -m "Initial commit - DevOps microservices project scaffold"

# 9️⃣ Push to GitHub
git push -u origin main
