---
theme: default
background: https://static.pingcap.com/files/2023/06/01173743/illus-scalable-1.svg
title: TiDB 101
info: |
  ## TiDB 101
  30 SQL Queries to Get You Understood TiDB Optimizer
class: text-center
highlighter: shiki
drawings:
  persist: false
transition: slide-left
mdc: true
---

# Welcome to TiDB 101

30 SQL Queries to Get You Understood TiDB Optimizer

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Press Space for next page <carbon:arrow-right class="inline"/>
  </span>
</div>

<div class="abs-br m-6 flex gap-2">
  <button @click="$slidev.nav.openInEditor()" title="Open in Editor" class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon:edit />
  </button>
  <a href="https://github.com/hi-rustin/TiDB-101" target="_blank" alt="GitHub" title="Open in GitHub"
    class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
    <carbon-logo-github />
  </a>
</div>

---
transition: slide-up
---

# SQL 1


```sql
mysql> SELECT 1;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)

mysql> SELECT 1 FROM DUAL;
+---+
| 1 |
+---+
| 1 |
+---+
1 row in set (0.00 sec)
```

---
transition: slide-up
---

# Hash Join
Create two tables and insert data:

```sql
create table t1 (a int, b int, key(a));
create table t2 (a int, b int, key(a));
insert into t1 values (1, 1), (2, 2), (3, 3);
insert into t2 values (1, 1), (2, 2), (3, 3);
analyze table t1, t2;
```

---
transition: slide-up
---

# Hash Join
Check query plan:

```sql
mysql> explain select * from t1, t2 where t1.a = t2.a;
+------------------------------+---------+-----------+---------------+----------------------------------------------+
| id                           | estRows | task      | access object | operator info                                |
+------------------------------+---------+-----------+---------------+----------------------------------------------+
| HashJoin_37                  | 3.00    | root      |               | inner join, equal:[eq(test.t1.a, test.t2.a)] |
| ├─TableReader_55(Build)      | 3.00    | root      |               | data:Selection_54                            |
| │ └─Selection_54             | 3.00    | cop[tikv] |               | not(isnull(test.t2.a))                       |
| │   └─TableFullScan_53       | 3.00    | cop[tikv] | table:t2      | keep order:false                             |
| └─TableReader_49(Probe)      | 3.00    | root      |               | data:Selection_48                            |
|   └─Selection_48             | 3.00    | cop[tikv] |               | not(isnull(test.t1.a))                       |
|     └─TableFullScan_47       | 3.00    | cop[tikv] | table:t1      | keep order:false                             |
+------------------------------+---------+-----------+---------------+----------------------------------------------+
7 rows in set (0.00 sec)
```
