from pyspark.sql import SparkSession

# Initialize Spark session
spark = SparkSession.builder.appName("DatabricksExceptionExample").getOrCreate()

# Sample DataFrame
data = [("John", 25), ("Doe", -5), ("Jane", 30)]
columns = ["name", "age"]

df = spark.createDataFrame(data, columns)

# Check for records where age is negative
negative_ages_count = df.filter(df["age"] < 0).count()

if negative_ages_count > 0:
    raise ValueError(f"Found {negative_ages_count} records with negative ages!")

# If no exception is raised, you can continue with other operations
df.show()
