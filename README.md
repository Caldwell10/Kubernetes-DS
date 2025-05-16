# Widgetario Microservices App (Kubernetes Hackathon)

## 📌 Overview

**Widgetario** is a distributed **microservices web-based application** designed to manage product inventories and stock levels for a gadget selling company.  
It comprises several interconnected services deployed in a **Kubernetes environment using Docker containers**.  
Its **3 main components** are:  
**-Products API**: Fetches product data from a PostgreSQL database.  
**-Stock API**: Handles stock-related queries and caches data locally.  
**-Web App**: Frontend application that interacts with the APIs.  
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

git clone https://github.com/Caldwell10/Kubernetes-DS.git  

**2.Create the Namespace**  

kubectl create namespace widgetario  

**3.Apply ConfigMap**  

kubectl apply -f web-config.yaml -n widgetario  

**4.Deploy the App(Apply each manifest)**  

kubectl apply -f products-db.yaml -n widgetario  
kubectl apply -f products-api.yaml -n widgetario  
kubectl apply -f stock-api.yaml -n widgetario  
kubectl apply -f web.yaml -n widgetario  

**5.Expose Web UI**  

kubectl get svc -n widgetario  

**Then access via:**

http://localhost:(web-node-port)  

## **Usage Guidelines**  

**After deployment, verify services using:**

kubectl get pods -n widgetario  

**Test endpoints using a temporary pod:**

kubectl run tester --image=busybox:1.28 --rm -it --restart=Never -n widgetario -- sh  

**From inside tester, check:**

wget -qO- http://products-api  
wget -qO- http://stock-api:8080  
wget -qO- http://web:8080


## **Environment Variables & Configuration**
**ConfigMap (web-config) is mounted into /app/secrets/api.json in the web pod.**
Example:

{
  "ProductsApiBase": "http://products-api",  
  "StockApiBase": "http://stock-api:8080"
}

**Database password is passed via environment variable:**

- name: POSTGRES_PASSWORD  
  value: widgetario123
