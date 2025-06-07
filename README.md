# Python Snowpark Reference Card

## Session Management

|Category      |Function                            |Description            |Example                                                        |Notes                      |
|--------------|------------------------------------|-----------------------|---------------------------------------------------------------|---------------------------|
|**Connection**|`Session.builder.configs().create()`|Create Snowpark session|`session = Session.builder.configs(connection_params).create()`|Required for all operations|
|**Connection**|`session.close()`                   |Close session          |`session.close()`                                              |Always close when done     |
|**Connection**|`session.sql()`                     |Execute SQL directly   |`session.sql("SELECT * FROM table").show()`                    |Returns DataFrame          |

## DataFrame Creation

|Category    |Function                    |Description             |Example                                                                 |Notes             |
|------------|----------------------------|------------------------|------------------------------------------------------------------------|------------------|
|**Creation**|`session.table()`           |Reference existing table|`df = session.table("database.schema.table")`                           |Most common method|
|**Creation**|`session.create_dataframe()`|Create from data        |`df = session.create_dataframe([[1,'a'],[2,'b']], schema=['id','name'])`|For local data    |
|**Creation**|`session.read.csv()`        |Read CSV from stage     |`df = session.read.csv("@stage/file.csv")`                              |Stage must exist  |
|**Creation**|`session.read.json()`       |Read JSON from stage    |`df = session.read.json("@stage/file.json")`                            |Auto-parses JSON  |
|**Creation**|`session.read.parquet()`    |Read Parquet from stage |`df = session.read.parquet("@stage/file.parquet")`                      |Columnar format   |

## DataFrame Operations

|Category       |Function            |Description            |Example                                     |Notes                    |
|---------------|--------------------|-----------------------|--------------------------------------------|-------------------------|
|**Selection**  |`.select()`         |Select columns         |`df.select("col1", "col2")`                 |Use col() for expressions|
|**Selection**  |`.select(col())`    |Select with expressions|`df.select(col("price") * 0.1)`             |Import col from functions|
|**Filtering**  |`.filter()`         |Filter rows            |`df.filter(col("age") > 18)`                |Use col() for conditions |
|**Filtering**  |`.where()`          |Alias for filter       |`df.where(col("status") == "active")`       |Same as filter()         |
|**Aggregation**|`.group_by()`       |Group data             |`df.group_by("category").agg(sum("amount"))`|Chain with agg()         |
|**Aggregation**|`.agg()`            |Aggregate functions    |`df.agg(avg("price"), max("date"))`         |Multiple aggregations    |
|**Sorting**    |`.sort()`           |Sort DataFrame         |`df.sort(col("date").desc())`               |Use asc()/desc()         |
|**Sorting**    |`.order_by()`       |Alias for sort         |`df.order_by("name", col("age").desc())`    |Multiple columns         |
|**Joining**    |`.join()`           |Join DataFrames        |`df1.join(df2, df1.id == df2.user_id)`      |Inner join default       |
|**Joining**    |`.join(type="left")`|Left join              |`df1.join(df2, condition, "left")`          |Specify join type        |

## Column Operations

|Category         |Function        |Description      |Example                            |Notes                |
|-----------------|----------------|-----------------|-----------------------------------|---------------------|
|**Column Ref**   |`col()`         |Reference column |`col("column_name")`               |Import from functions|
|**Renaming**     |`.alias()`      |Rename column    |`col("old_name").alias("new_name")`|Use in select()      |
|**Renaming**     |`.as_()`        |Alias alternative|`col("price").as_("unit_price")`   |Same as alias()      |
|**Casting**      |`.cast()`       |Cast data type   |`col("string_col").cast("int")`    |Type conversion      |
|**Null Handling**|`.is_null()`    |Check for null   |`col("field").is_null()`           |Returns boolean      |
|**Null Handling**|`.is_not_null()`|Check not null   |`col("field").is_not_null()`       |Opposite of is_null  |

## Built-in Functions

|Category       |Function             |Description         |Example                                                        |Notes                  |
|---------------|---------------------|--------------------|---------------------------------------------------------------|-----------------------|
|**Aggregate**  |`sum()`              |Sum values          |`df.agg(sum("amount"))`                                        |Import from functions  |
|**Aggregate**  |`avg()`              |Average             |`df.agg(avg("score"))`                                         |Numerical columns      |
|**Aggregate**  |`count()`            |Count rows          |`df.agg(count("*"))`                                           |Use “*” for all rows   |
|**Aggregate**  |`max()`              |Maximum value       |`df.agg(max("date"))`                                          |Works with dates       |
|**Aggregate**  |`min()`              |Minimum value       |`df.agg(min("price"))`                                         |Any comparable type    |
|**String**     |`upper()`            |Convert to uppercase|`df.select(upper(col("name")))`                                |String function        |
|**String**     |`lower()`            |Convert to lowercase|`df.select(lower(col("text")))`                                |String function        |
|**String**     |`substring()`        |Extract substring   |`df.select(substring(col("text"), 1, 5))`                      |Start position, length |
|**String**     |`length()`           |String length       |`df.select(length(col("description")))`                        |Character count        |
|**String**     |`trim()`             |Remove whitespace   |`df.select(trim(col("name")))`                                 |Leading/trailing spaces|
|**Date/Time**  |`current_date()`     |Current date        |`df.select(current_date())`                                    |System date            |
|**Date/Time**  |`current_timestamp()`|Current timestamp   |`df.select(current_timestamp())`                               |System datetime        |
|**Date/Time**  |`date_add()`         |Add days to date    |`df.select(date_add(col("date"), 30))`                         |Add specified days     |
|**Date/Time**  |`datediff()`         |Date difference     |`df.select(datediff("day", col("start"), col("end")))`         |Units: day, month, year|
|**Math**       |`abs()`              |Absolute value      |`df.select(abs(col("difference")))`                            |Removes negative sign  |
|**Math**       |`round()`            |Round number        |`df.select(round(col("price"), 2))`                            |Decimal places         |
|**Conditional**|`when()`             |Conditional logic   |`df.select(when(col("age") >= 18, "Adult").otherwise("Minor"))`|If-then-else           |
|**Conditional**|`coalesce()`         |First non-null      |`df.select(coalesce(col("phone"), col("mobile"), lit("N/A")))` |Null handling          |
|**Literal**    |`lit()`              |Literal value       |`df.select(lit("constant_value"))`                             |Add constants          |

## DataFrame Actions

|Category   |Function      |Description      |Example                  |Notes               |
|-----------|--------------|-----------------|-------------------------|--------------------|
|**Display**|`.show()`     |Display DataFrame|`df.show(20)`            |Default 10 rows     |
|**Display**|`.collect()`  |Collect to local |`rows = df.collect()`    |Returns Row objects |
|**Display**|`.to_pandas()`|Convert to Pandas|`pd_df = df.to_pandas()` |Brings data locally |
|**Info**   |`.count()`    |Count rows       |`total_rows = df.count()`|Triggers computation|
|**Info**   |`.columns`    |Get column names |`col_names = df.columns` |Returns list        |
|**Info**   |`.schema`     |Get schema       |`df.schema`              |Column types        |
|**Info**   |`.explain()`  |Show query plan  |`df.explain()`           |Debug performance   |

## Window Functions

|Category  |Function               |Description       |Example                                          |Notes                      |
|----------|-----------------------|------------------|-------------------------------------------------|---------------------------|
|**Window**|`Window.partition_by()`|Partition window  |`Window.partition_by("category")`                |Import Window              |
|**Window**|`Window.order_by()`    |Order window      |`Window.order_by(col("date").desc())`            |Required for some functions|
|**Window**|`row_number()`         |Row number        |`df.select(row_number().over(window_spec))`      |Ranking function           |
|**Window**|`rank()`               |Rank with gaps    |`df.select(rank().over(window_spec))`            |Handles ties               |
|**Window**|`dense_rank()`         |Dense rank        |`df.select(dense_rank().over(window_spec))`      |No gaps in ranking         |
|**Window**|`lag()`                |Previous row value|`df.select(lag(col("price")).over(window_spec))` |Access previous row        |
|**Window**|`lead()`               |Next row value    |`df.select(lead(col("price")).over(window_spec))`|Access next row            |

## Data Manipulation

|Category     |Function            |Description          |Example                                                                |Notes                |
|-------------|--------------------|---------------------|-----------------------------------------------------------------------|---------------------|
|**Transform**|`.with_column()`    |Add/modify column    |`df.with_column("new_col", col("price") * 0.1)`                        |Single column        |
|**Transform**|`.with_columns()`   |Add/modify multiple  |`df.with_columns({"tax": col("price")*0.1, "total": col("price")*1.1})`|Multiple columns     |
|**Transform**|`.drop()`           |Remove columns       |`df.drop("unwanted_col")`                                              |Can drop multiple    |
|**Transform**|`.rename()`         |Rename column        |`df.rename(col("old_name"), "new_name")`                               |Single rename        |
|**Sampling** |`.sample()`         |Sample rows          |`df.sample(0.1)`                                                       |10% sample           |
|**Dedup**    |`.distinct()`       |Remove duplicates    |`df.distinct()`                                                        |All columns          |
|**Dedup**    |`.drop_duplicates()`|Remove duplicates    |`df.drop_duplicates(["col1", "col2"])`                                 |Specific columns     |
|**Union**    |`.union()`          |Union DataFrames     |`df1.union(df2)`                                                       |Must have same schema|
|**Union**    |`.union_all()`      |Union with duplicates|`df1.union_all(df2)`                                                   |Includes duplicates  |

## Writing Data

|Category|Function                  |Description   |Example                                            |Notes           |
|--------|--------------------------|--------------|---------------------------------------------------|----------------|
|**Save**|`.write.save_as_table()`  |Save as table |`df.write.save_as_table("new_table")`              |Creates table   |
|**Save**|`.write.mode("overwrite")`|Overwrite mode|`df.write.mode("overwrite").save_as_table("table")`|Replace existing|
|**Save**|`.write.mode("append")`   |Append mode   |`df.write.mode("append").save_as_table("table")`   |Add to existing |
|**Copy**|`.copy_into_table()`      |Copy to table |`df.copy_into_table("target_table")`               |Bulk insert     |

## User-Defined Functions (UDFs)

|Category|Function                |Description         |Example                                                      |Notes                   |
|--------|------------------------|--------------------|-------------------------------------------------------------|------------------------|
|**UDF** |`@udf`                  |Python UDF decorator|`@udf(return_type=IntegerType())\ndef my_func(x): return x*2`|Import from types       |
|**UDF** |`session.udf.register()`|Register UDF        |`session.udf.register(my_func, return_type=IntegerType())`   |Alternative registration|
|**UDTF**|`@udtf`                 |Table function      |`@udtf(output_schema=["col1", "col2"])\nclass MyUDTF:...`    |Returns multiple rows   |

## Stored Procedures

|Category     |Function                  |Description       |Example                                                                     |Notes             |
|-------------|--------------------------|------------------|----------------------------------------------------------------------------|------------------|
|**Procedure**|`@sproc`                  |Stored procedure  |`@sproc(return_type=StringType())\ndef my_proc(session, x): return "result"`|Runs on Snowflake |
|**Procedure**|`session.sproc.register()`|Register procedure|`session.sproc.register(my_proc, return_type=StringType())`                 |Alternative method|

## Best Practices & Tips

|Category          |Technique        |Description           |Example                                         |Notes                    |
|------------------|-----------------|----------------------|------------------------------------------------|-------------------------|
|**Performance**   |Lazy evaluation  |Operations are lazy   |`df.filter(...).select(...)`                    |No execution until action|
|**Performance**   |Push down filters|Filter early          |`df.filter(condition).select(cols)`             |Reduces data processed   |
|**Performance**   |Use stages       |For file operations   |`session.file.put("local.csv", "@stage")`       |Upload then read         |
|**Debugging**     |Use explain()    |Check query plan      |`df.explain()`                                  |Understand execution     |
|**Memory**        |Avoid collect()  |Keep data in Snowflake|Use `.show()` instead of `.collect()`           |Prevents OOM errors      |
|**Error Handling**|Try-catch blocks |Handle exceptions     |`try: df.show() except Exception as e: print(e)`|Graceful error handling  |

## Common Import Statements

```python
from snowflake.snowpark import Session
from snowflake.snowpark.functions import *
from snowflake.snowpark.types import *
from snowflake.snowpark import Window
```

## Session Configuration Example

```python
connection_params = {
    "account": "your_account",
    "user": "your_user", 
    "password": "your_password",
    "warehouse": "your_warehouse",
    "database": "your_database",
    "schema": "your_schema"
}

session = Session.builder.configs(connection_params).create()
```

## Complex Query Example

```python
# Complex aggregation with window functions
result = (df
    .filter(col("date") >= "2023-01-01")
    .with_column("month", date_trunc("month", col("date")))
    .group_by("category", "month")
    .agg(sum("amount").alias("monthly_total"))
    .with_column("running_total", 
                sum("monthly_total").over(
                    Window.partition_by("category")
                          .order_by("month")
                          .rows_between(Window.UNBOUNDED_PRECEDING, Window.CURRENT_ROW)
                ))
    .sort("category", "month")
)
```