import sqlite3
import pandas as pd
import matplotlib.pyplot as plt

# Step 1: Create and populate the database
conn = sqlite3.connect("/mnt/data/sales_data.db")
cursor = conn.cursor()

# Create the sales table
cursor.execute('''
    CREATE TABLE IF NOT EXISTS sales (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        product TEXT,
        quantity INTEGER,
        price REAL
    )
''')

# Insert sample data
sample_data = [
    ("Apples", 10, 1.5),
    ("Bananas", 20, 0.75),
    ("Oranges", 15, 1.2),
    ("Apples", 5, 1.5),
    ("Bananas", 10, 0.75),
    ("Oranges", 10, 1.2)
]
cursor.execute("DELETE FROM sales")  # Clear existing data for repeatability
cursor.executemany("INSERT INTO sales (product, quantity, price) VALUES (?, ?, ?)", sample_data)

# Step 2: Query the database
query = """
SELECT product, 
       SUM(quantity) AS total_qty, 
       SUM(quantity * price) AS revenue 
FROM sales 
GROUP BY product
"""
df = pd.read_sql_query(query, conn)

# Step 3: Plot the chart
plt.figure(figsize=(6, 4))
df.plot(kind='bar', x='product', y='revenue', legend=False, color='skyblue')
plt.title("Revenue per Product")
plt.xlabel("Product")
plt.ylabel("Revenue")
plt.tight_layout()

# Save the chart as a PNG image
output_path = "/mnt/data/sales_chart.png"
plt.savefig(output_path)

# Close the connection
conn.close()

output_path
