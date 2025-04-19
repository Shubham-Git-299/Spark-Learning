# Spark Submit Command - Cheat Sheet

## 🚀 What is `spark-submit`?
`spark-submit` is the command-line tool to launch Spark applications. It submits your application code to a Spark cluster and allows fine-grained control over job resources and configuration.

---

## 🧱 Basic Syntax
```bash
spark-submit \
  --class <main-class> \
  --master <master-url> \
  --deploy-mode <client|cluster> \
  --executor-memory <mem> \
  --executor-cores <cores> \
  --num-executors <num> \
  [other options] \
  <your-application.jar or .py> \
  [application arguments]
```

---

## 📌 Common Options
| Option | Description |
|--------|-------------|
| `--class` | Main class (required for JAR apps) |
| `--master` | Cluster manager (e.g. `local[*]`, `yarn`, `spark://<host>:<port>`) |
| `--deploy-mode` | Where the driver runs: `client` (local) or `cluster` (on cluster) |
| `--executor-memory` | Memory per executor (e.g. `4G`) |
| `--driver-memory` | Memory for the driver process |
| `--executor-cores` | Cores per executor |
| `--num-executors` / `--conf spark.executor.instances` | Number of executors |
| `--conf` | Additional configs like shuffle partitions, timeouts, etc. |

---

## ✅ Example 1: Submit JAR to YARN
```bash
spark-submit \
  --class com.example.MyApp \
  --master yarn \
  --deploy-mode cluster \
  --executor-memory 4G \
  --num-executors 10 \
  --executor-cores 4 \
  --conf spark.sql.shuffle.partitions=100 \
  my-spark-app.jar arg1 arg2
```

## ✅ Example 2: Submit PySpark Script Locally
```bash
spark-submit \
  --master local[*] \
  --executor-memory 2G \
  my_script.py
```

---

## ✅ Example 3: Custom Cluster Deployment (PySpark)
```bash
spark-submit \
 --master spark://10.0.0.1:7077 \
 --deploy-mode client \
 --executor-memory 4G \
 --driver-memory 2G \
 --conf spark.app.name=WordCountApp \
 --conf spark.executor.cores=2 \
 --conf spark.executor.instances=5 \
 --conf spark.default.parallelism=20 \
 --conf spark.driver.maxResultSize=1G \
 --conf spark.network.timeout=800 \
 --py-files /path/to/other/python/files.zip \
 /path/to/your/python/wordcount.py \
 /path/to/input/textfile.txt
```

### 🔍 Explanation:
- `--master spark://10.0.0.1:7077`: Connects to a standalone Spark cluster at this address
- `--deploy-mode client`: The driver runs on the submitting machine
- `--executor-memory 4G`: Each executor gets 4GB of RAM
- `--driver-memory 2G`: The driver gets 2GB of RAM
- `--conf spark.app.name=WordCountApp`: Names the app in the Spark UI
- `--conf spark.executor.cores=2`: Each executor uses 2 CPU cores
- `--conf spark.executor.instances=5`: Starts 5 executors
- `--conf spark.default.parallelism=20`: Sets default number of partitions
- `--conf spark.driver.maxResultSize=1G`: Limits result size the driver can collect
- `--conf spark.network.timeout=800`: Increases network timeout for long tasks
- `--py-files`: Additional Python files zipped and shipped to executors
- Final line: Python script and input text file for WordCount logic

---

## 🧠 Tips
- Use `--deploy-mode cluster` for production jobs
- Always monitor your job via **Spark UI**
- Tune number of executors, memory, and cores based on data size and cluster capacity
- Avoid collecting large datasets to driver to prevent `OutOfMemory` errors

---

Let me know if you want Kubernetes or EMR examples too! ✅

