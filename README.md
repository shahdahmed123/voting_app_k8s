# 🗳️ Voting App on Kubernetes

This project deploys a **Voting Application** on Kubernetes using multiple microservices, each running in its own Pod.  
The stack includes **frontend, backend, worker, database, cache, services, and ingress**.

---

## 🚀 Architecture

The application is split into 5 main components:

1. **Vote App (Frontend)**  
   - A simple web app (Python/Flask).  
   - Allows users to vote between two options (e.g., 🐶 Dogs vs 🐱 Cats).  
   - Exposed to users via **Kubernetes Service + Ingress**.

2. **Redis (In-Memory Store)**  
   - Stores incoming votes temporarily.  
   - Used as a **queue** for fast writes.

3. **Worker**  
   - A background processor (written in .NET/C#).  
   - Consumes votes from Redis and stores results in Postgres.

4. **Postgres (Database)**  
   - Stores the final, persistent voting results.  
   - Data can survive restarts via **PersistentVolume**.

5. **Result App (Frontend)**  
   - A Node.js web app.  
   - Reads results from Postgres and displays them in real-time.

---

## 🏗️ Kubernetes Resources

- **Deployments** → Manage Pods for each component (vote, result, worker, redis, postgres).  
- **Services** → Internal/external communication between components.  
- **Ingress** → Routes external traffic to the vote and result apps.  
- **ConfigMap / Secret** → Store environment variables and DB credentials.  
- **PersistentVolumeClaim** → Store Postgres data permanently.

---

## 📌 High-Level Diagram

```plaintext
               ┌─────────────────────┐
               │      Ingress        │
               └─────────┬───────────┘
                         │
        ┌────────────────┼────────────────┐
        │                                 │
┌───────▼───────┐                 ┌───────▼───────┐
│   Vote App    │                 │  Result App   │
│ (Python/Flask)│                 │ (Node.js)     │
└───────┬───────┘                 └───────┬───────┘
        │                                 │
        │                                 │
┌───────▼───────┐                 ┌───────▼───────┐
│     Redis     │   <── Worker ─► │   Postgres    │
│ (Queue)       │                 │ (Database)    │
└───────────────┘                 └───────────────┘
