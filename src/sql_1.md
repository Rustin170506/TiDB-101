# Day1

## Env

Based on: <https://github.com/hi-rustin/tidb/tree/tidb-101>

## SQL

```sql
SELECT 1;
```

## Debug

1. Find a place to write a unit test:

    <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/executor/tidb_101_TEST.go#L9>

    ```go
    func TestDay1(t *testing.T) {
        store := testkit.CreateMockStore(t)
        tk := testkit.NewTestKit(t, store)
        tk.MustQuery("SELECT 1").Check(testkit.Rows("1"))
    }
    ```

2. Set a breakpoint in the unit test:

    ```go
    func TestDay1(t *testing.T) {
        store := testkit.CreateMockStore(t)
        tk := testkit.NewTestKit(t, store)
        tk.MustQuery("SELECT 1").Check(testkit.Rows("1")) // Set a breakpoint here
    }
    ```

3. In our test framework, we will parse it as statement node first: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/testkit/testkit.go#L348>
4. Then we can execute it: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/session.go#L2125>
   1. Try to compile it to a execute statement: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/session.go#L2211>
      1. During compilation, we can focus on planner: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/executor/compiler.go#L100>
         1. In the planner, we call a internal function to build a plan: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/optimize.go#L468>
         2. It involves two main steps:
            1. Build a logical plan: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/optimize.go#L495>
                1. This function will check the ast node type and call different functions to build a plan: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/planbuilder.go#L812>
                2. Because our statement is a `SelectStmt`, it will call this function: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/logical_plan_builder.go#L4293>
                3. In this function, because our select is pretty simple, the only thing it does is to build a `buildTableRefs`: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/logical_plan_builder.go#L4377>
                   1. Because it is a dummy table, so it always only has one row: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/logical_plan_builder.go#L4645>
                   2. We want to select `1` from the dummy table, so we need to build a `Projection`: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/logical_plan_builder.go#L4517>
                   3. We will go through all the fields and convert them to `Column` expression: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/logical_plan_builder.go#L1773>
                   4. Finally, we finish building the `Projection` and return it: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/logical_plan_builder.go>
            2. Do the optimization and build a physical plan: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/optimize.go#L532>
               1. In this function, we will can `DoOptimizeAndLogicAsRet` to do the optimization. It has three steps: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/optimizer.go#L306>
                  1. First, we do some logical optimization. This step tries to apply some rules to the logical plan to make it more efficient: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/optimizer.go#L321>
                  2. Second, we do some physical optimization. It is cost based optimization and try to find the best physical plan: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/optimizer.go#L333>
                  3. Last, we do some post optimization. It is some optimization that can only be done after we have a physical plan: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/planner/core/optimizer.go#L337>
               2. Because our plan is pretty simple, we don't have any optimization here. So we don't need to dig into it too much.
   2. The next step involves executing a statement, which can be done using this method: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/session.go#L2271>
5. We get a `RecordSet` after executing the statement: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/session.go#L2300>
6. Then we can fetch the result from the `RecordSet`: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/testkit/testkit.go#L177>
   1. We call other API `ResultSetToStringSlice` from session to fetch the result: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/tidb.go#L367>
   2. It will call `GetRows4Test` to get the result: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/tidb.go>
   3. In `GetRows4Test`, we need to allocate a `chunk` to store the result: <https://github.com/hi-rustin/tidb/blob/tidb-101/pkg/session/tidb.go#L347>
