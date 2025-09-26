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

ت
