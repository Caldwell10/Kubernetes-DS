# Widgetario Microservices App (Kubernetes Hackathon)

## 📌 Overview

**Widgetario** is a distributed **microservices web-based application** designed to manage product inventories and stock levels for a gadget selling company.  
It comprises several interconnected services deployed in a **Kubernetes environment using Docker containers**.  
Its **3 main components** are:  
**-Products API(Java Spring Boot service)**: Fetches product data from a PostgreSQL database.  
**-Stock API(Golang microservice)**: Handles stock-related queries and caches data locally.  
**-Web App(ASP.NET Core frontend)**: Frontend application that interacts with the APIs.  
This project was built as part of a Kubernetes hackathon and demonstrates key DevOps and cloud-native concepts such as **container orchestration, service discovery, environment configuration, and inter-service communication**.  

## **Features**

~Scalable and containerized microservices architecture with clear separation of concerns  
~Persistent PostgreSQL database with optional master-secondary replication  
~API caching with /cache persistence  
~Ingress-enabled domain routing: widgetario.local and api.widgetario.local  
~Prometheus + Grafana observability dashboard  
~GitHub Actions for CI/CD  
~Tested in Docker Desktop with Kubernetes  

## **Installation and Deployment**  

**Prerequisites**  

~Kubernetes cluster  
~kubectl configured  
~Docker Desktop  
~Git  
~Internet connection (to pull images from Docker Hub)  

**1.Clone the repository**  

```
git clone https://github.com/Caldwell10/Kubernetes-DS.git
```

**2.Create the Namespace**  

```
kubectl create namespace widgetario
```  

**3.Apply ConfigMap**  

```
kubectl apply -f web-config.yaml -n widgetario
```

**4.Deploy the App(Apply each manifest)**  

```
kubectl apply -f products-db.yaml -n widgetario  
kubectl apply -f products-api.yaml -n widgetario  
kubectl apply -f stock-api.yaml -n widgetario  
kubectl apply -f web.yaml -n widgetario
```  

**5.Expose Web UI**  

```
kubectl get svc -n widgetario
```

**Then access via:**

```
http://localhost:(web-node-port)
```

## **Usage Guidelines**  

**After deployment, verify services using:**

```
kubectl get pods -n widgetario
```

**Test endpoints using a temporary pod:**

```
kubectl run tester --image=busybox:1.28 --rm -it --restart=Never -n widgetario -- sh
```

**From inside tester, check:**

```
wget -qO- http://products-api  
wget -qO- http://stock-api:8080  
wget -qO- http://web:8080
```


## **Environment Variables & Configuration**
**ConfigMap (web-config) is mounted into /app/secrets/api.json in the web pod.**
Example:

```
{
  "ProductsApiBase": "http://products-api",  
  "StockApiBase": "http://stock-api:8080"
}
```

**Database password is passed via environment variable:**

```
- name: POSTGRES_PASSWORD  
  value: widgetario123
```

## Deploy to Kubernetes
**Deploy Part 3: Storage & Replication**
```
kubectl delete deploy products-db
kubectl delete svc products-db
kubectl delete pvc -l app=products-db
kubectl apply -f hackathon/solution-part-3/products-db \
               -f hackathon/solution-part-3/products-api \
               -f hackathon/solution-part-3/stock-api
kubectl rollout restart deploy/products-api deploy/stock-api
```

**Deploy Part 4: Ingress**
```
kubectl apply -f hackathon/solution-part-4/ingress-controller \
               -f hackathon/solution-part-4/products-db \
               -f hackathon/solution-part-4/products-api \
               -f hackathon/solution-part-4/web
```

**Update hosts file**
```
# Windows
./scripts/add-to-hosts.ps1 widgetario.local 127.0.0.1
./scripts/add-to-hosts.ps1 api.widgetario.local 127.0.0.1
# Linux/macOS
./scripts/add-to-hosts.sh widgetario.local 127.0.0.1
./scripts/add-to-hosts.sh api.widgetario.local 127.0.0.1
```

 **Access the app**

```
Web App: http://widgetario.local
Products API: http://api.widgetario.local/products
```

**Enable observability**
```
kubectl apply -f hackathon/solution-part-6/prometheus \
               -f hackathon/solution-part-6/grafana
```
**Load Grafana**
```
hackathon/files/grafana-dashboard.json
```

**Example API Calls**
```
curl http://api.widgetario.local/products
curl http://api.widgetario.local/stocks
curl http://api.widgetario.local/actuator/prometheus
```

## **Cleanup**
**To delete all resources:**

```
kubectl delete all --all -n widgetario  
kubectl delete namespace widgetario
```

## Screenshots

## CI/CD
**You can integrate GitHub Actions to auto-deploy changes:**

**.github/workflows/deploy.yml:**

```
name: Deploy to Kubernetes  
on:  
  push:  
    branches: [ main ]

jobs:  
  deploy:  
    runs-on: ubuntu-latest  
    steps:  
    - uses: actions/checkout@v3  
    - name: Set up kubectl  
      uses: azure/setup-kubectl@v3  
    - name: Deploy manifests  
      run: |  
        kubectl apply -f k8s/
```  

## License
This project is licensed under the MIT License.
