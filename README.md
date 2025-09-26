# 🗳️ Voting App on Kubernetes

This project deploys a **Voting Application** on Kubernetes using multiple microservices, each running in its own Pod.  
The stack includes **frontend, backend, worker, database, and cache**.

---

## 🚀 Architecture

The application is split into 5 main components:

1. **Vote App (Frontend)**  
   - A Python/Flask web app.  
   - Allows users to vote between two options (e.g., 🐶 Dogs vs 🐱 Cats).  
   - Exposed using a **NodePort Service** (default: `30001`).

2. **Redis (In-Memory Store)**  
   - Stores incoming votes temporarily.  
   - Works as a **queue** for fast writes.

3. **Worker**  
   - A background processor (written in .NET/C#).  
   - Consumes votes from Redis and stores results in Postgres.

4. **Postgres (Database)**  
   - Stores the final, persistent voting results.  
   - Data persists with a **PersistentVolumeClaim (PVC)**.

5. **Result App (Frontend)**  
   - A Node.js web app.  
   - Reads results from Postgres and displays them in real-time.  
   - Exposed using a **NodePort Service** (default: `30002`).

---

## 🏗️ Kubernetes Resources

- **Deployments** → Manage Pods for each component (vote, result, worker, redis, postgres).  
- **Services (NodePort/ClusterIP)** → Handle communication between components.  
- **ConfigMap / Secret** → Store environment variables and DB credentials.  
- **PersistentVolumeClaim** → Store Postgres data permanently.  
- **NetworkPolicy** → (Optional) Restrict communication paths.

---

## 📌 High-Level Diagram

```plaintext
               ┌─────────────────────────┐
               │        Users            │
               └──────────┬──────────────┘
                          │
            ┌─────────────┼──────────────┐
            │                            │
   ┌────────▼────────┐          ┌────────▼────────┐
   │   Vote App      │          │   Result App    │
   │ (NodePort 30001)│          │ (NodePort 30002)│
   └───────┬─────────┘          └────────┬────────┘
           │                             │
           │                             │
   ┌───────▼────────┐           ┌────────▼────────┐
   │     Redis      │ <──Worker─┤    Postgres     │
   │ (ClusterIP)    │           │ (PVC Attached)  │
   └────────────────┘           └────────────────┘
ChatGPT said:

تمام 👌
مدام إنتي ماعملتيش Ingress في المشروع، يبقى نخلي الـ README أبسط ونشيل حتة الـ Ingress ونخلي الدخول على الـ Apps عن طريق NodePort Services.

هجهزلك نسخة جديدة من README.md تناسب شغلك الحالي 👇

# 🗳️ Voting App on Kubernetes

This project deploys a **Voting Application** on Kubernetes using multiple microservices, each running in its own Pod.  
The stack includes **frontend, backend, worker, database, and cache**.

---

## 🚀 Architecture

The application is split into 5 main components:

1. **Vote App (Frontend)**  
   - A Python/Flask web app.  
   - Allows users to vote between two options (e.g., 🐶 Dogs vs 🐱 Cats).  
   - Exposed using a **NodePort Service** (default: `30001`).

2. **Redis (In-Memory Store)**  
   - Stores incoming votes temporarily.  
   - Works as a **queue** for fast writes.

3. **Worker**  
   - A background processor (written in .NET/C#).  
   - Consumes votes from Redis and stores results in Postgres.

4. **Postgres (Database)**  
   - Stores the final, persistent voting results.  
   - Data persists with a **PersistentVolumeClaim (PVC)**.

5. **Result App (Frontend)**  
   - A Node.js web app.  
   - Reads results from Postgres and displays them in real-time.  
   - Exposed using a **NodePort Service** (default: `30002`).

---

## 🏗️ Kubernetes Resources

- **Deployments** → Manage Pods for each component (vote, result, worker, redis, postgres).  
- **Services (NodePort/ClusterIP)** → Handle communication between components.  
- **ConfigMap / Secret** → Store environment variables and DB credentials.  
- **PersistentVolumeClaim** → Store Postgres data permanently.  
- **NetworkPolicy** → (Optional) Restrict communication paths.

---

## 📌 High-Level Diagram

```plaintext
               ┌─────────────────────────┐
               │        Users            │
               └──────────┬──────────────┘
                          │
            ┌─────────────┼──────────────┐
            │                            │
   ┌────────▼────────┐          ┌────────▼────────┐
   │   Vote App      │          │   Result App    │
   │ (NodePort 30001)│          │ (NodePort 30002)│
   └───────┬─────────┘          └────────┬────────┘
           │                             │
           │                             │
   ┌───────▼────────┐           ┌────────▼────────┐
   │     Redis      │ <──Worker─┤    Postgres     │
   │ (ClusterIP)    │           │ (PVC Attached)  │
   └────────────────┘           └────────────────┘

⚡ Getting Started
1. Clone the repo
git clone https://github.com/Remo11mo/voting-app_k8s.git
cd voting-app_k8s

2. Create Namespace
kubectl apply -f namespace.yaml

3. Deploy all components
kubectl apply -f db-deployment.yaml
kubectl apply -f redis-deployment.yaml
kubectl apply -f vote-deployment.yaml
kubectl apply -f result-deployment.yaml
kubectl apply -f worker-deployment.yaml

4. Check resources
kubectl get all -n voting-app

5. Access the App

Voting App → http://<NodeIP>:30001

Result App → http://<NodeIP>:30002

🛠️ Technologies Used

Kubernetes (Deployments, Services, PVC, NetworkPolicy)

Docker (Containerization)

Python / Flask (Vote App)

Node.js (Result App)

Redis (Queue)

Postgres (Database)

.NET Worker (Processor)

👩‍💻
