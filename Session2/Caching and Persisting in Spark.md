# cache() vs persist() in Apache Spark

## 🚀 Purpose
Both `cache()` and `persist()` are used to **store RDDs or DataFrames** in memory to **avoid recomputation**. This is crucial for iterative algorithms and performance optimization.

---

## 🧠 Basic Difference

| Feature        | `cache()`                            | `persist()`                        |
|----------------|----------------------------------------|-------------------------------------|
| Behavior       | Stores in memory, fallback to disk    | Customizable storage levels         |
| Default Mode   | MEMORY_AND_DISK                       | Needs explicit level if not default |
| Flexibility    | ❌ Fixed storage level                 | ✅ Flexible                         |
| Serialization  | ❌ No (unless using *_SER levels)     | ✅ Optional with *_SER levels       |

---

## 🧾 What is `cache()`?

```scala
df.cache()
```

- Shorthand for:
  ```scala
  df.persist(StorageLevel.MEMORY_AND_DISK)
  ```
- Stores as much as possible in **memory**.
- If not enough memory, **spills to disk**.
- Automatically **reuses** the stored result in subsequent actions.

---

## 🧾 What is `persist()`?

```scala
import org.apache.spark.storage.StorageLevel

df.persist(StorageLevel.MEMORY_ONLY)
```

- More **flexible** and allows different storage options.
- You can store:
  - Only in memory
  - Only on disk
  - Serialized or un-serialized
  - With or without replication

---

## 🔍 When to Use What

| Use Case                             | Method       | Why                                        |
|--------------------------------------|--------------|---------------------------------------------|
| Default memory + disk fallback       | `cache()`    | Simple, quick, covers common case           |
| Memory-only strategy                 | `persist()`  | If recomputation is cheap                   |
| Memory is limited, avoid failures    | `persist()`  | Use DISK_ONLY or MEMORY_AND_DISK_SER        |
| Need serialization to save space     | `persist()`  | Use *_SER to reduce memory footprint        |

---

## 📦 Popular Storage Levels

| Storage Level                    | Description                                                       |
|----------------------------------|-------------------------------------------------------------------|
| MEMORY_ONLY                      | Store only in memory, recompute if not enough                     |
| MEMORY_AND_DISK                  | Try memory, spill to disk if needed (default for `cache()`)       |
| MEMORY_ONLY_SER                 | Serialized in memory, saves space but more CPU                    |
| MEMORY_AND_DISK_SER             | Serialized + spill to disk                                        |
| DISK_ONLY                        | Store only on disk                                                |
| OFF_HEAP (if enabled)            | Store in off-heap memory (requires Spark config change)           |

---

## ✅ Best Practices
- Use `cache()` when starting out or for prototyping
- Use `persist()` with specific levels for tuning performance/memory
- Monitor storage usage via **Spark UI → Storage tab**
- Don’t forget to `unpersist()` when done:
  ```scala
  df.unpersist()
  ```

