
![image](https://github.com/user-attachments/assets/d9a03c15-f8dd-46fc-9049-0245e18d3d6f)

# Apache Spark Architecture

## 🔧 Key Components of Spark Architecture

### 1. Driver Program
- **What it is**: Your main Spark application – the entry point of any Spark job.
- **What it does**:
  - Converts user code into tasks.
  - Maintains the **SparkContext** – the connection to the Spark cluster.
  - Coordinates the job execution across the cluster.

# 🧠 What is SparkContext?

- **SparkContext** is the **starting point** for using Spark.
- It creates a **connection to the Spark cluster**.
- You use it to:
  - Create **RDDs** (Resilient Distributed Datasets),
  - Create **accumulators** (for counting things),
  - Share **broadcast variables** (common read-only data across nodes).

🔹 Driver Program = The entire application  
🔸 SparkContext = The brain of that application
---

### 2. Cluster Manager
- **What it is**: The resource manager (can be **YARN**, **Mesos**, **Kubernetes**, or **Standalone**).
- **What it does**:
  - Allocates resources (CPU, memory) across the cluster.
  - Launches **Executors** on worker nodes.

---

### 3. Workers / Executor Nodes
- **What it is**: The actual worker machines in the cluster.
- **What it does**:
  - Run the **executors**, which:
    - Execute code assigned by the driver.
    - Store data in memory/disk.
    - Communicate with the driver.

---

### 4. Executors
- **What they are**: JVM processes launched on worker nodes.
- **What they do**:
  - Execute tasks in parallel.
  - Store data using Spark’s in-memory caching.
  - Return results to the Driver.

---

### 5. Tasks
- **What they are**: The smallest units of work sent to executors.
- **What they do**: Perform operations on data (like map, filter, join).

---

### 6. Job, Stage, and Task Hierarchy
- **Job**: Triggered by an action (e.g., `.collect()`, `.show()`).
- **Stages**: Jobs are divided into stages based on shuffle boundaries.
- **Tasks**: Stages are further split into multiple parallel tasks.

---

## ⚙️ How Spark Works (Execution Flow)

1. **Driver sends a job** (e.g., `df.show()`) to the SparkContext.
2. The job is **split into DAG stages** (Directed Acyclic Graph).
3. Stages are **divided into tasks**, based on partitions.
4. **Cluster Manager** allocates executors on worker nodes.
5. **Executors perform tasks**, process data, cache if needed.
6. Results are **sent back to the driver**, or persisted.

---

## 🧠 Spark Execution Model Summary
- **Transformations** (like `map`, `filter`) are lazy and build the DAG.
- **Actions** (like `collect`, `count`) trigger execution.
- The DAG Scheduler plans execution, breaking it into stages.
- The Task Scheduler sends tasks to executors.

<img width="641" alt="image" src="https://github.com/user-attachments/assets/0c6552c2-c6d3-4a7b-90fb-e7f868f96020" />

# 🚀 Spark Deployment Modes – Client vs Cluster

---

## 🔍 What is Deployment Mode?

Deployment mode in Spark defines **where the Driver Program runs** when a Spark job is submitted to the cluster.

---

## 🧱 Core Spark Architecture (Same in Both Modes)

- **Driver Program**: Controls job execution and scheduling.
- **Cluster Manager** (e.g., YARN): Allocates resources.
- **Executors**: Run tasks and process data.
- **Tasks**: Smallest units of work sent to executors.

---

## 🧑‍💻 Client Mode

- When you launch a **Spark Shell** or use `--deploy-mode client`, the **Driver runs on your local machine**.
- SparkSession is created on the client machine (your laptop or edge node).
- Driver requests the **YARN Resource Manager** to start a **YARN Application**.
- YARN launches an **Application Master (AM)** container in the cluster.
- In Client mode:
  - **Application Master** acts as an **Executor launcher** only.
  - It asks the Resource Manager to start **executor containers**.
  - **Executors communicate directly with the Driver** (on your machine).

 <img width="636" alt="image" src="https://github.com/user-attachments/assets/549e4d14-c521-4873-b297-cb354faf9c57" />


### 📌 Summary of Client Mode

| Component        | Location                    |
|------------------|-----------------------------|
| Driver           | Local Machine               |
| Application Master | Cluster                   |
| Executors        | Cluster                     |

---

## 🖥️ Cluster Mode

- When you use `--deploy-mode cluster`, the **Driver runs inside the cluster**.
- Resource Manager starts an **Application Master (AM)**.
- The **Application Master creates the Driver** inside it.
- The Driver then communicates with Resource Manager to request executor containers.
- **Executors communicate with the Driver** running in the Application Master (not your local machine).

- <img width="674" alt="image" src="https://github.com/user-attachments/assets/ba860205-418e-4f38-9f01-258e17748b79" />


### 📌 Summary of Cluster Mode

| Component        | Location                      |
|------------------|-------------------------------|
| Driver           | Inside the cluster (in AM)     |
| Application Master | Cluster                     |
| Executors        | Cluster                       |

---

## ⚖️ Comparison Table

| Feature             | Client Mode                          | Cluster Mode                             |
|---------------------|---------------------------------------|-------------------------------------------|
| Driver Location     | Local Machine                        | Inside the Cluster                        |
| Dependency on Local | High (client must stay alive)        | Low (client can disconnect)               |
| Best For            | Dev, testing, debugging              | Production, large & long-running jobs     |
| Network Traffic     | Executors → Driver on local machine  | Executors → Driver in cluster             |

---

## ✅ Final Summary

- The **architecture remains the same**; only the **Driver location changes**.
- **Client Mode**: Driver runs locally, good for dev/testing.
- **Cluster Mode**: Driver runs on the cluster, ideal for production.



