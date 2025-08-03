# An End-to-End Example

Inside of the data folder, you’ll see that we have a number of files. 
Each file has a number of rows within it. These files are CSV files, meaning that they’re a semi-
structured data format, with each row in the file representing a row in our future DataFrame.

```bash
head end-to-end_example/data/2015-summary.csv
```

Output:
```bash
DEST_COUNTRY_NAME,ORIGIN_COUNTRY_NAME,count
United States,Romania,15
United States,Croatia,1
United States,Ireland,344
Egypt,United States,15
United States,India,62
United States,Singapore,1
United States,Grenada,62
Costa Rica,United States,588
Senegal,United States,40
```
## Transformation
Spark takes a small portion of the data and then tries to parse the types in those rows in accordance with the types that Spark has accessible in order to obtain the schema information.  When reading in data, you can also choose to tightly specify a schema, which is what we advise in production circumstances.</p>
Scala:
```scala
val df = spark.read 
    .option("inferschema","true") 
    .option("header","true")
    .csv("/home/batuhansaylam/Desktop/Big-Data_Spark/end-to-end_example/data/2015-summary.csv")
```
Output:
```bash
val df: org.apache.spark.sql.DataFrame = [DEST_COUNTRY_NAME: string, ORIGIN_COUNTRY_NAME: string ... 1 more field]
```
Python:
```python
df = spark.read.option("inferschema","true").option("header","true").csv("/home/batuhansaylam/Desktop/Big-Data_Spark/end-to-end_example/data/2015-summary.csv")
```
- **.read**: This method returns a DataFrameReader object, which allows us to use chaining methods to structure the data reading process.

- **.option()**: This method is used to set various options for reading data. It takes two arguments: the option's name and its value.

  - **"inferschema"**: This option tells Spark to automatically infer the column types (schema) when reading data.

  - **"true"**: By setting the inferschema option to true, we tell Spark to examine the first few lines of the file and try to guess whether each column is an Integer, String, Double, etc. If this option were false, Spark would read all columns as String (text) by default, and we would have to manually convert the types ourselves.
- **.option()**: Another option setting method.

  - **"header"**: This option specifies that the first row of the file should be the column headers, not the data.

  - **"true"**: By setting the header option to true, we tell Spark to use the first row of the CSV file as the column names and not read this row as data.

- **.csv("/home/batuhansaylam/Desktop/Big-Data_Spark/end-to-end_example/data/2015-summary.csv")** : This is a method of the DataFrameReader object and tells Spark that the file format to be read is CSV (Comma-Separated Values).

## Action

If we perform the take action on the DataFrame, we will be able to see the same results that we
saw before when we used the command line</p>
Scala:
```scala
df.take(3)
```

Output:
```bash
val res2: Array[org.apache.spark.sql.Row] = Array([United States,Romania,15], [United States,Croatia,1], [United States,Ireland,344])
```
Python:
```python
df.take(3)
```

Output: 
```bash
[Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Romania', count=15), Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Croatia', count=1), Row(DEST_COUNTRY_NAME='United States', ORIGIN_COUNTRY_NAME='Ireland', count=344)]
```

Let’s specify some more transformations! Now, let’s sort our data according to the count
column, which is an integer type</p>

Remember, sort does not modify the DataFrame. We use sort as a transformation that returns a new
DataFrame by transforming the previous DataFrame.</p>
Scala:
```scala
df.sort("count").explain()
```
Python:
```python
df.sort("count").explain()
```
Output:
```bash
== Physical Plan ==
AdaptiveSparkPlan isFinalPlan=false
+- Sort [count#19 ASC NULLS FIRST], true, 0
   +- Exchange rangepartitioning(count#19 ASC NULLS FIRST, 200), ENSURE_REQUIREMENTS, [plan_id=33]
      +- FileScan csv [DEST_COUNTRY_NAME#17,ORIGIN_COUNTRY_NAME#18,count#19] Batched: false, DataFilters: [], Format: CSV, Location: InMemoryFileIndex(1 paths)[file:/home/batuhansaylam/Desktop/Big-Data_Spark/end-to-end_example/dat..., PartitionFilters: [], PushedFilters: [], ReadSchema: struct<DEST_COUNTRY_NAME:string,ORIGIN_COUNTRY_NAME:string,count:int>
```