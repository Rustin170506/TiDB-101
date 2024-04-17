---
theme: seriph
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
