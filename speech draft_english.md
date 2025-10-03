# Pandas: Data Cleaning & Analysis Complete Speech (English Version)

---

## **Video Opening**

**(0:00 - 0:30)**

Hello everyone, and welcome to today's course. My name is Yihan. Over the next 45 minutes or so, we'll explore how to use Pandas, a powerful Python library, to transform messy, scattered data into a clean, organized format and extract valuable business insights. Whether you're a beginner in data analysis or a developer looking to systematically review core Pandas skills, this course will provide you with a solid foundation.

---

## **Section 1: Introduction & Scenario**

**(0:30 - 3:30)**

Let's not jump straight into code. Imagine this scenario: you're a newly hired Data Analyst at a global e-commerce company. On your first day, your boss gives you a task: "In our European market, which product category has the highest actual sales revenue? And make sure to exclude returned and cancelled orders."

You get the data, but it's not in great shape. It's scattered across three different files: a CSV file for transaction records, a JSON file with customer information, and an Excel spreadsheet containing product details. To make matters worse, this data is full of missing values and inconsistent formatting, and most critically—the sales revenue field doesn't even exist! You'll need to calculate it yourself from quantity, unit price, and discount.

To tackle this, we'll follow a clear, eight-step analysis workflow.
First, we will **load** these data files from their different formats.
Second, we'll **inspect** the data structure to understand what we're working with.
Third, we'll **clean** the missing information and formatting issues from the data.
Fourth, we'll perform **feature engineering** to calculate the crucial sales revenue metric.
Fifth, we'll **filter** the data to focus only on valid European market transactions.
Sixth, we'll **sort** the data to discover key trends.
Seventh, we will **merge** these three separate files into a single, unified dataset.
And finally, the most crucial step, we will perform **grouping and aggregation** on this clean data to precisely answer our boss's question.

---

## **Section 2: Module 1 - Loading & Inspecting Data**

**(3:30 - 10:00)**

Alright, let's get started. Real-world data exists in various formats, and the first superpower of Pandas is its ability to convert these different file types into a standardized, two-dimensional table structure called a "DataFrame." It's like a universal data translator.

Let's start by loading our first file, the sales records in CSV format. We'll use the `pd.read_csv()` function.

```python
import pandas as pd

# Load sales records
sales_df = pd.read_csv('sales_records.csv')
```

CSV stands for "Comma-Separated Values," and it's one of the most common data formats. This function is very powerful and has many optional parameters. For example, `sep` can be used to specify different delimiters, and `usecols` allows us to select only the columns we need during loading, which can save a lot of memory when working with large files.

Next, we'll use `pd.read_excel()` to load the product information. Excel files often contain multiple worksheets, so we need to use the `sheet_name` parameter to specify which one to read.

```python
# Load product information
product_df = pd.read_excel('product_info.xlsx', sheet_name='ProductDetails')
```

Finally, let's load the customer data using `pd.read_json()`. JSON is a key-value pair format commonly used in web APIs. Pandas can even load JSON data directly from a URL.

```python
# Load customer information
customer_df = pd.read_json('customer_details.json')
```

Now that the data is loaded, we can't just blindly trust it. A professional data analyst always performs a quick "health check" on the data first. We'll use three very useful methods: `.head()`, `.info()`, and `.describe()`.

```python
# Preview the first few rows of sales data
print("Sales Data Head:")
print(sales_df.head())

# View data structure and type information
print("\nSales Data Info:")
sales_df.info()

# View statistical summary of numeric columns
print("\nSales Data Statistics:")
print(sales_df.describe())
```

`.head()` lets us preview the first few rows of the data. But `.info()` is even more crucial. It tells us the data type of each column and—pay close attention here—the count of non-null values. By comparing the total number of entries with the non-null count, we can immediately spot which columns have missing values. You see, our data has 150 rows, but the `discount` column has only 90 non-null values, the `list_price` column has 18 missing values, and the `region` column has 3 missing values. These are exactly the problems we need to tackle next.

---

## **Section 3: Module 2 - Handling Missing Values**

**(10:00 - 20:00)**

Data cleaning is the most time-consuming yet most critical phase in data analysis. A professional data analyst will tell you they spend 80% of their time cleaning data. Why? Because dirty data leads to wrong conclusions, which in turn leads to poor business decisions.

Let's first count how many missing values we have in each column. Pandas uses `NaN` (Not a Number) to represent missing values.

```python
# Count missing values in each column
print("Missing values per column:")
print(sales_df.isna().sum())

# We can also see the missing value percentage
print("\nMissing value percentage:")
print((sales_df.isna().sum() / len(sales_df) * 100).round(2))
```

Good, now we clearly see the problem. Next, let me show you the two main strategies for handling missing values.

### **Strategy 1: Deletion - Using `dropna()`**

The first method is simple and direct: delete rows containing missing values. Let's see what happens if we use this method.

```python
# View original number of rows
print(f"Original dataset: {len(sales_df)} rows")

# Drop any rows with missing values
cleaned_df_dropped = sales_df.dropna()
print(f"After dropna(): {len(cleaned_df_dropped)} rows")
print(f"Lost {len(sales_df) - len(cleaned_df_dropped)} rows ({(len(sales_df) - len(cleaned_df_dropped))/len(sales_df)*100:.1f}%)")

# View data after dropping
print("\nData after dropping NaN:")
print(cleaned_df_dropped.info())
```

Do you see what happened? After using `dropna()`, we went from 150 rows down to about 50 rows, losing two-thirds of our data! This is the problem with `dropna()`—it's a double-edged sword.

When can we use `dropna()`?
- When the amount of missing data is very small (say, less than 5%)
- When we can confirm these rows are truly useless for analysis
- When the missing data is completely randomly distributed

But in our case, losing two-thirds of the data is clearly unacceptable. This would introduce serious bias and might systematically exclude important customer segments or product types. So we need a more refined approach.

### **Strategy 2: Filling - Using `fillna()`**

A more professional approach is to fill missing values with reasonable values. Different columns need different filling strategies. Let me demonstrate each one.

**Filling Strategy 1: Fill with a fixed value**

For the `discount` column, missing values represent no discount, so filling with 0 makes the most sense.

```python
# Method 1: Fill discount column with 0
# Create a copy to preserve original data
sales_df_copy = sales_df.copy()

print(f"Discount missing values before: {sales_df_copy['discount'].isna().sum()}")
sales_df_copy['discount'].fillna(0, inplace=True)
print(f"Discount missing values after: {sales_df_copy['discount'].isna().sum()}")
```

**Filling Strategy 2: Fill categorical data with "Unknown"**

For categorical data like the `region` column, missing values can be marked with "Unknown".

```python
# Method 2: Fill region column with "Unknown"
print(f"\nRegion missing values before: {sales_df_copy['region'].isna().sum()}")
sales_df_copy['region'].fillna('Unknown', inplace=True)
print(f"Region missing values after: {sales_df_copy['region'].isna().sum()}")

# View region distribution
print("\nRegion distribution after filling:")
print(sales_df_copy['region'].value_counts())
```

**Filling Strategy 3: Fill numeric data with mean**

For numeric columns like `list_price`, we can fill with the column's mean. This is particularly effective when the data is normally distributed.

```python
# Method 3: Fill list_price column with mean
print(f"\nList_price missing values before: {sales_df_copy['list_price'].isna().sum()}")

# Calculate mean
mean_price = sales_df_copy['list_price'].mean()
print(f"Mean list_price: ${mean_price:.2f}")

# Fill missing values
sales_df_copy['list_price'].fillna(mean_price, inplace=True)
print(f"List_price missing values after: {sales_df_copy['list_price'].isna().sum()}")
```

**Filling Strategy 4: Fill with median (more robust approach)**

However, the mean is susceptible to outliers. If the data contains anomalies, the median is usually a better choice. Let's compare them.

```python
# Method 4: Compare using median for filling
# Create another copy
sales_df_median = sales_df.copy()

# Calculate median
median_price = sales_df_median['list_price'].median()
print(f"\nMedian list_price: ${median_price:.2f}")
print(f"Mean list_price: ${mean_price:.2f}")
print(f"Difference: ${abs(mean_price - median_price):.2f}")

# Fill with median
sales_df_median['list_price'].fillna(median_price, inplace=True)

# Compare results of both filling methods
print("\nComparison of filling methods:")
print(f"Mean-filled data average: ${sales_df_copy['list_price'].mean():.2f}")
print(f"Median-filled data average: ${sales_df_median['list_price'].mean():.2f}")
```

You see, the median is more robust and isn't affected by outliers. In real-world work, if you're unsure about the data distribution, the median is usually the safer choice.

### **Best Practice: Use a dictionary to specify different filling strategies for different columns**

The most professional approach is to use a dictionary to specify the most appropriate fill value for multiple columns all at once.

```python
# Best practice: Use dictionary to handle all missing values at once
sales_df = sales_df.copy()

# Create fill values dictionary
fill_values = {
    'discount': 0,                                  # No discount: use 0
    'region': 'Unknown',                            # Unknown region: use Unknown
    'list_price': sales_df['list_price'].median()  # Price: use median
}

# Fill all columns at once
sales_df.fillna(value=fill_values, inplace=True)

# Verify results
print("\nFinal missing value check:")
print(sales_df.isna().sum())
print("\nData cleaning complete!")
```

Perfect! Now our data has no missing values, and we've retained all 150 rows without losing any information. This is the professional way to clean data.

Next, we need to address inconsistent formatting.

**Standardize the region column**

```python
# View unique values in the region column
print("Unique regions before cleaning:")
print(sales_df['region'].unique())

# Standardize region column: convert to lowercase and replace 'eu' with 'europe'
sales_df['region'] = sales_df['region'].str.lower().str.strip()
sales_df['region'] = sales_df['region'].replace('eu', 'europe')

print("\nUnique regions after cleaning:")
print(sales_df['region'].unique())
```

**Standardize the status column**

```python
# View unique values in the status column
print("\nUnique statuses before cleaning:")
print(sales_df['status'].unique())

# Standardize status column
sales_df['status'] = sales_df['status'].str.lower().str.strip()

# Create standardization mapping dictionary
status_mapping = {
    'shipped': 'Shipped',
    's': 'Shipped',
    'returned': 'Returned',
    'r': 'Returned',
    'pending': 'Pending',
    'p': 'Pending',
    'cancelled': 'Cancelled',
    'c': 'Cancelled'
}

sales_df['status'] = sales_df['status'].map(status_mapping)

print("\nUnique statuses after cleaning:")
print(sales_df['status'].unique())
```

**Standardize product_id column and check for duplicates**

```python
# Convert product_id to lowercase
sales_df['product_id'] = sales_df['product_id'].str.lower()
product_df['product_id'] = product_df['product_id'].str.lower()

# Check for duplicate rows
duplicates = sales_df.duplicated().sum()
print(f"\nNumber of duplicate rows: {duplicates}")

# If duplicates exist, remove them
if duplicates > 0:
    print("Duplicate rows found:")
    print(sales_df[sales_df.duplicated(keep=False)].sort_values('transaction_id'))
    sales_df.drop_duplicates(inplace=True)
    print(f"Removed {duplicates} duplicate rows")
    print(f"Final dataset: {len(sales_df)} rows")
```

---

## **Section 4: Module 3 - Feature Engineering**

**(20:00 - 24:00)**

Now we come to a very critical step—feature engineering. You may have noticed that our dataset doesn't have a `sales` (revenue) column. This actually reflects real-world scenarios: sales revenue is typically a derived field that analysts calculate from raw data.

In a real e-commerce environment, the formula for calculating final sales revenue is:
**final_sales = quantity × list_price × (1 - discount)**

This formula accounts for the quantity purchased, the list price, and any applicable discount. Let's create this new column.

```python
# Create the final_sales column
sales_df['final_sales'] = sales_df['quantity'] * sales_df['list_price'] * (1 - sales_df['discount'])

# View the results
print("Data with calculated sales:")
print(sales_df[['transaction_id', 'quantity', 'list_price', 'discount', 'final_sales']].head(10))

# View sales statistics
print("\nSales statistics:")
print(sales_df['final_sales'].describe())
```

This is the power of feature engineering! We've created a new, more meaningful metric from existing columns. This `final_sales` column will be the core of our subsequent analysis.

---

## **Section 5: Module 4 - Filtering & Selecting Data**

**(24:00 - 29:00)**

Our data is clean and ready, but it contains information from all regions worldwide and orders of all statuses. Our boss's question is clear: focus only on valid orders in the European market. We need to perform precise data filtering.

In Pandas, the most common filtering method is called "boolean indexing." We can combine multiple conditions to filter data precisely. First, we only want data from the European region. Second, we want to exclude Returned and Cancelled orders, as these don't represent actual sales.

```python
# Method 1: Multi-condition filtering using boolean indexing
valid_europe_sales = sales_df[
    (sales_df['region'] == 'europe') & 
    (sales_df['status'].isin(['Shipped', 'Pending']))
]

print(f"Total transactions: {len(sales_df)}")
print(f"Valid Europe transactions: {len(valid_europe_sales)}")
print(f"Percentage: {len(valid_europe_sales)/len(sales_df)*100:.1f}%")
print(f"\nSample of filtered data:")
print(valid_europe_sales.head())
```

Here we used the `&` operator to combine conditions, and the `.isin()` method to check if the status is in our specified valid list. Note that each condition is enclosed in parentheses, which is important.

If we also want to select specific columns, the most professional approach is to use the `.loc` accessor. Its syntax is `df.loc[row_condition, [list_of_column_names]]`.

```python
# Method 2: Use .loc to filter rows and select columns simultaneously
europe_key_data = sales_df.loc[
    (sales_df['region'] == 'europe') & 
    (sales_df['status'].isin(['Shipped', 'Pending'])),
    ['transaction_id', 'customer_id', 'product_id', 'final_sales', 'sale_date']
]

print(europe_key_data.head())
```

An important reason to use `.loc` is that it helps avoid the `SettingWithCopyWarning`. This approach makes your intention clear and is the officially recommended best practice.

Let's also look at the orders we filtered out to get a complete picture of the data.

```python
# View the distribution of excluded order statuses
excluded_orders = sales_df[~sales_df['status'].isin(['Shipped', 'Pending'])]
print("\nExcluded order status distribution:")
print(excluded_orders['status'].value_counts())
print(f"\nTotal excluded: {len(excluded_orders)} orders")
print(f"Excluded revenue: ${excluded_orders['final_sales'].sum():,.2f}")
```

---

## **Section 6: Module 5 - Sorting Data**

**(29:00 - 34:00)**

Filtering helped us get the right subset of data, but it's often unordered. Sorting allows us to discover patterns and trends in the data. Pandas provides two main sorting functions, and let me demonstrate their differences and uses in detail.

### **Sort by Values - `sort_values()`**

The first and most commonly used function is `sort_values()`, which sorts based on the actual values in one or more columns.

**Single column sorting:**

```python
# Sort by sales in descending order to find the highest transactions
top_sales = valid_europe_sales.sort_values(by='final_sales', ascending=False)

print("Top 10 transactions by sales value:")
print(top_sales[['transaction_id', 'product_id', 'quantity', 'final_sales']].head(10))

# We can also look at the lowest
print("\nBottom 5 transactions by sales value:")
print(top_sales[['transaction_id', 'product_id', 'quantity', 'final_sales']].tail(5))
```

**Multi-column sorting:**

The power of `sort_values()` lies in its ability to sort by multiple columns simultaneously. This is very useful when you need multi-level sorting.

```python
# Multi-level sort: first by product ID, then by sales
multi_sorted = valid_europe_sales.sort_values(
    by=['product_id', 'final_sales'],
    ascending=[True, False]  # product_id ascending, final_sales descending
)

print("\nMulti-level sort (by product_id, then by final_sales):")
print(multi_sorted[['transaction_id', 'product_id', 'final_sales', 'sale_date']].head(15))

# This way we can see the highest-value transaction for each product
```

Note that the `ascending` parameter can be a list, allowing us to specify ascending or descending order for each column separately. This gives us tremendous flexibility.

### **Sort by Index - `sort_index()`**

The second function is `sort_index()`, which doesn't look at the data values but sorts based on the row index labels. When is this useful? Let me demonstrate several scenarios.

**Scenario 1: Restore original order**

When data has been filtered and sorted multiple times, the index may become jumbled. Using `sort_index()` can restore the original row order.

```python
# View current index order (already jumbled after sorting)
print("Current index after sorting:")
print(top_sales.index[:20].tolist())

# Use sort_index to restore original order
restored_order = top_sales.sort_index()
print("\nIndex after sort_index():")
print(restored_order.index[:20].tolist())

# Now the data is arranged in the original CSV file order
print("\nData in original order:")
print(restored_order[['transaction_id', 'final_sales']].head())
```

**Scenario 2: When the index itself is meaningful**

Sometimes we set a meaningful column as the index, such as dates or product IDs. In these cases, `sort_index()` is particularly useful.

```python
# Set sale_date as the index
date_indexed = valid_europe_sales.copy()
date_indexed['sale_date'] = pd.to_datetime(date_indexed['sale_date'])
date_indexed = date_indexed.set_index('sale_date')

print("\nBefore sorting by date index:")
print(date_indexed[['transaction_id', 'final_sales']].head())

# Sort by date index
date_sorted = date_indexed.sort_index()
print("\nAfter sorting by date index:")
print(date_sorted[['transaction_id', 'final_sales']].head())

# Can also sort descending (newest dates first)
date_sorted_desc = date_indexed.sort_index(ascending=False)
print("\nDate sorted descending (newest first):")
print(date_sorted_desc[['transaction_id', 'final_sales']].head())
```

### **Summary: `sort_values()` vs `sort_index()`**

Simply put:
- **`sort_values()`**: Sort by column data values → Used to find maximum, minimum, rankings
- **`sort_index()`**: Sort by row index labels → Used to restore order, arrange by time series

Both functions support the `ascending` parameter to control ascending or descending order, and both support `inplace=True` to modify the original DataFrame directly.

---

## **Section 7: Module 6 - Merging & Joining Data**

**(34:00 - 39:00)**

Now we come to a crucial step. We have three separate data tables: sales records, product information, and customer information. To answer "which product category has the highest sales revenue," we must connect the product category information with the sales data. This is where data merging comes in.

The core function for this task in Pandas is `pd.merge()`. We'll perform the merge step by step. First, merge the sales data with the product data.

```python
# Step 1: Merge valid Europe sales data with product data
merged_df = pd.merge(
    valid_europe_sales,
    product_df,
    on='product_id',
    how='left'
)

print("After merging with product data:")
print(merged_df.head())
print(f"\nShape: {merged_df.shape}")

# Check if any product information is missing
missing_products = merged_df['category'].isna().sum()
print(f"Transactions with missing product info: {missing_products}")
```

Here, `on='product_id'` specifies the common column for joining. `how='left'` means we're using a left join, keeping all rows from the left table (sales records). If a product isn't found in the product table, the related product information columns will show as NaN.

Next, we'll merge the result with customer data to get the complete analytical dataset.

```python
# Step 2: Merge the previous result with customer data
full_df = pd.merge(
    merged_df,
    customer_df,
    on='customer_id',
    how='left'
)

print("\nFinal merged dataset:")
print(full_df.head())
print(f"\nFinal shape: {full_df.shape}")
print(f"Columns: {list(full_df.columns)}")
```

Let me quickly explain the different merge types. Understanding these is crucial for handling data correctly.

| Join Type (`how`) | Analogy | Data Retained | Use Case |
|:---|:---|:---|:---|
| `inner` | Intersection of two circles | Only rows with matching keys in **both** tables | Only care about completely matched data |
| `left` | All of the left circle | All rows from the **left** table, and matched rows from the right | Preserve integrity of main table (e.g., all sales records) |
| `right` | All of the right circle | All rows from the **right** table, and matched rows from the left | Similar to left, but main table is on the right |
| `outer` | Union of two circles | All rows from **both** tables, with NaN for non-matching parts | Need to see all data, including unmatched |

In our case, using a `left` join ensures we don't lose any sales records, even if some product or customer information might be missing.

---

## **Section 8: Module 7 - Grouping & Aggregation**

**(39:00 - 45:30)**

Excellent! We now finally have a single, clean, unified, and complete dataset. We are ready to answer our boss's original question: which product category has the highest sales revenue in the European market? To do this, we'll use one of the most powerful ideas in data analysis: the "Split-Apply-Combine" strategy.

This strategy is simple: first, we **split** the data into groups based on some criteria (like product category). Then, we **apply** a calculation to each group (like a sum). Finally, we **combine** all the results into a new summary table.

In Pandas, this is achieved with the `groupby()` function. Let's find the total sales for each product category in the European market.

```python
# Group by product category and calculate the sum of sales for each
category_sales = full_df.groupby('category')['final_sales'].sum()

# Sort the results to find the top-selling categories
top_categories = category_sales.sort_values(ascending=False)

print("Sales by Category (Europe, Valid Orders):")
print(top_categories)
print(f"\nTop-selling category: {top_categories.index[0]}")
print(f"Revenue: ${top_categories.iloc[0]:,.2f}")
```

Let's break down this key line of code: `full_df.groupby('category')` performs the "split," grouping data by category; `['final_sales']` selects the column we want to operate on; and `.sum()` is the aggregation function we "apply" to calculate the sum for each group.

A powerful feature of `groupby()` is that we can use the `.agg()` method to apply multiple aggregation functions at once. Let's create a more comprehensive analysis report.

```python
# Apply multiple aggregations to each category
category_summary = full_df.groupby('category').agg(
    total_sales=('final_sales', 'sum'),
    average_sale=('final_sales', 'mean'),
    number_of_transactions=('transaction_id', 'count'),
    average_discount=('discount', 'mean'),
    max_single_sale=('final_sales', 'max')
).round(2)

# Sort by total sales
category_summary = category_summary.sort_values(by='total_sales', ascending=False)

print("\nComprehensive Category Analysis Report:")
print(category_summary)
```

This "named aggregation" syntax is very clean and readable, and it's a modern Pandas best practice. Now, we've not only answered our boss's question but also provided much richer insights: the average order value for each category, number of transactions, average discount rates, and even the highest single transaction.

Let's do one more interesting analysis: looking at the sales distribution across different order statuses.

```python
# Analyze orders by status (using the full dataset, not just Europe)
status_analysis = sales_df.groupby('status').agg(
    transaction_count=('transaction_id', 'count'),
    total_revenue=('final_sales', 'sum'),
    avg_order_value=('final_sales', 'mean')
).round(2)

print("\nOrder Status Analysis:")
print(status_analysis)

# Calculate the proportion of valid orders
total_transactions = len(sales_df)
valid_transactions = len(sales_df[sales_df['status'].isin(['Shipped', 'Pending'])])
print(f"\nValid order ratio: {valid_transactions/total_transactions*100:.1f}%")

# Calculate potential revenue lost due to returns and cancellations
lost_revenue = sales_df[sales_df['status'].isin(['Returned', 'Cancelled'])]['final_sales'].sum()
print(f"Revenue lost to returns and cancellations: ${lost_revenue:,.2f}")
```

---

## **Section 9: Conclusion & Practice Challenge**

**(45:30 - 47:00)**

We did it! Let me recap our complete journey. We started with three messy, scattered files containing missing values, inconsistent formatting, and not even having the core sales revenue field. By following a professional data analysis workflow, we completed eight key steps:

1. **Load data** - Import from CSV, Excel, and JSON formats
2. **Inspect data** - Use .head(), .info(), and .describe() to understand data structure
3. **Clean data** - Compare dropna() and fillna(), using multiple filling strategies (0, "Unknown", mean, median)
4. **Feature engineering** - Calculate final_sales = quantity × list_price × (1 - discount)
5. **Filter data** - Keep only valid orders from the European region
6. **Sort data** - Master the difference between sort_values() and sort_index()
7. **Merge data** - Join three tables into a unified dataset
8. **Group and aggregate** - Analyze by category to derive the final answer

This process is the universal workflow that data analysts use to solve problems with Pandas.

I want to emphasize several key takeaways from today's course:

**First**, handling missing values is an art, not a science. `dropna()` is simple but dangerous and may lose large amounts of data. `fillna()` is more flexible but requires choosing the right filling strategy based on business logic.

**Second**, sorting seems simple, but understanding the difference between `sort_values()` and `sort_index()` is important. The former sorts by data values for analysis, while the latter sorts by index labels to restore order.

**Third**, feature engineering is a core data analysis skill. Real-world data often doesn't directly give you the metrics you want—you need to create them yourself.

**Fourth**, grouping and aggregation are powerful tools for answering business questions. Once you master groupby() and agg(), you can answer almost any "which...most..." type of question.

---

## **Video Closing**

**(47:00 - 47:30)**

Thank you for watching. I hope this course has been helpful for you. Please be sure to complete the practice activity, as hands-on experience is the best way to learn. Data analysis is not a theoretical subject, but a skill that requires extensive practice. If you have any questions, feel free to contact me. Remember, every professional data analyst started by cleaning their first CSV file. Keep going, and see you next time!