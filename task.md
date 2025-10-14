## **Pandas Data Analysis Exercise: North American Market Customer Analysis**

### **Exercise Tasks**

Please complete the data analysis tasks following these steps:

#### **Part 1: Data Loading and Inspection**
1. Load three data files:
   - `sales_records.csv`
   - `product_info.xlsx` (sheet name: 'ProductDetails')
   - `customer_details.json`
2. Use `.head()`, `.info()`, and `.describe()` to inspect each dataset
3. Count the number and percentage of missing values in each dataset

#### **Part 2: Data Cleaning**
1. Handle missing values in `sales_records`:
   - `discount` column: fill with 0
   - `region` column: fill with 'Unknown'
   - `list_price` column: fill with the **median** of the column
2. Standardize data formats:
   - Normalize `region` column to lowercase, and unify 'us', 'usa' to 'north_america'
   - Unify 'ca', 'canada' to 'north_america'
   - Standardize the `status` column (refer to the mapping method in the video)
   - Normalize all `product_id` to lowercase
3. Check for and remove duplicate rows

#### **Part 3: Feature Engineering**
1. Create `final_sales` column: `final_sales = quantity × list_price × (1 - discount)`
2. Create `order_month` column: extract month from `sale_date` (Hint: convert to datetime first)
3. Create `is_discounted` column: True if `discount > 0`, otherwise False

#### **Part 4: Data Filtering**
1. Filter data for the North American market (region == 'north_america')
2. Keep only valid orders (status is 'Shipped' or 'Pending')
3. Create two subsets:
   - `usa_sales`: orders where customer country is 'USA'
   - `canada_sales`: orders where customer country is 'Canada'

#### **Part 5: Data Sorting**
1. Find the top 10 transactions with the highest sales amount in the North American market
2. Perform multi-level sorting by customer ID and sale date
3. Find the 10 most recent transactions (sorted by date in descending order)
