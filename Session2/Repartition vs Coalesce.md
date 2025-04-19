# 🔄 Repartition vs Coalesce in Apache Spark

Understanding the difference between `repartition()` and `coalesce()` is important for optimizing performance and managing partition sizes in Spark jobs.

---

## 🔁 repartition()

### ✅ Key Points:
- Used to **increase or decrease** number of partitions.
- **Triggers a full shuffle** of data across the network.
- Ensures **balanced partitions** (roughly equal size).
- Ideal for **increasing parallelism** when cluster has more resources.
- You can **partition based on a column/key** for optimization.

### 📦 Example:
```scala
df.repartition(10)
df.repartition(col("user_id"))
```

### ⚠️ Cost:
- Expensive due to **full network shuffle**.

---

## 📉 coalesce()

### ✅ Key Points:
- Used to **reduce** the number of partitions.
- Tries to **avoid full shuffle** by merging existing partitions.
- Less expensive compared to `repartition()`.
- May lead to **data skew** if too many partitions are merged.

### 📦 Example:
```scala
df.coalesce(2)
```

### ⚠️ Cost:
- More efficient but **can cause skew** if not used carefully.

---

## ⚖️ Comparison Table

| Feature                      | repartition()                         | coalesce()                           |
|------------------------------|----------------------------------------|--------------------------------------|
| Use Case                    | Increase or rebalance partitions      | Reduce partitions                     |
| Shuffle                     | ✅ Full shuffle                        | ❌ Avoids full shuffle                |
| Performance                 | ❗ Expensive                           | ✅ More efficient                     |
| Data Skew Risk              | ❌ Low                                 | ⚠️ High if not careful               |
| Parallelism Gain            | ✅ Yes                                 | ❌ Not ideal for increasing tasks     |
| Custom Partitioning         | ✅ Yes (can specify column)            | ❌ No                                 |

---

## ✅ Best Practices
- Use `repartition()` when:
  - You need more tasks for better parallelism.
  - You want even distribution across partitions.
  - You need to repartition by a key/column.

- Use `coalesce()` when:
  - Reducing number of partitions (e.g., before saving to file).
  - Performance is a concern and shuffle should be avoided.

📌 Always monitor Spark UI to analyze partition sizes and job execution behavior!

