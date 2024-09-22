## $match (Filters documents)
   Purpose: Filters the documents to match certain criteria.
   Real-time Use: Filtering orders by status (e.g., only shipped orders).
```js  
   db.orders.aggregate([
   { $match: { status: "shipped" } }
   ]);
```
   Result: Returns documents where the status is shipped.
## $group (Groups data by a field)
Purpose: Groups documents by a field and applies aggregate functions like sum, avg, count, etc.
Real-time Use: Calculate total sales and number of items sold for each product.
```js
db.orders.aggregate([
  { $group: { _id: "$product", totalSales: { $sum: "$sales" }, totalItems: { $sum: "$quantity" } } }
   ]);
```
   Result: For each product, it returns total sales and quantity sold.
## $project (Selects specific fields)
   Purpose: Allows you to include, exclude, or rename fields.
   Real-time Use: Exclude some sensitive fields from results, such as hiding customer emails.
```jss
   db.customers.aggregate([
   { $project: { name: 1, email: 0 } } // Includes only the name, excludes the email
   ]);
```
   Result: Shows only the names, hiding email addresses.
## $lookup (Performs a join with another collection)
   Purpose: Similar to SQL joins, it allows you to merge documents from another collection.
   Real-time Use: Retrieve customer details for each order.
```js
   db.orders.aggregate([
   { $lookup: {
   from: "customers",
   localField: "_id",
   foreignField: "order_id",
   as: "customerDetails"
   }
   }
   ]);
```
   Result: Joins orders with customers collection based on the order ID.
## $sort (Sorts documents)
   Purpose: Sorts the documents by a specific field.
   Real-time Use: Sort orders by total sales in descending order.
```js  
   db.orders.aggregate([
   { $sort: { sales: -1 } } // -1 for descending, 1 for ascending
   ]);
```
   Result: Orders sorted by highest sales.
## $unwind (Deconstructs an array field)
Purpose: Splits array fields into separate documents.
Real-time Use: If an order contains multiple items in an array, each item is transformed into its own document.
 
```js
db.orders.aggregate([
  { $unwind: "$items" } // Assuming each order has an array "items"
   ]);
```
   Result: Each item in an array gets its own document.
## $limit and $skip (Limit and skip documents)
   Purpose: Limits or skips a certain number of documents.
   Real-time Use: Use it for pagination when fetching data in a web app.
    
```js
   db.orders.aggregate([
   { $sort: { orderDate: -1 } },
   { $skip: 10 }, // Skip the first 10 documents
   { $limit: 5 } // Then return the next 5 documents
   ]);
```
   Result: Useful for pagination, retrieves the 11th to 15th orders sorted by date.
## $addFields (Adds new fields to documents)
Purpose: Adds computed or derived fields to the documents.
Real-time Use: Add a field for calculating sales tax.
 
```js
db.orders.aggregate([
  { $addFields: { salesTax: { $multiply: ["$sales", 0.08] } } } // Assuming 8% tax rate
   ]);
   Result: Each document now has a salesTax field.
```

## Additional Practice Aggregation Pipeline
Here’s a combined pipeline using multiple stages:

Filter orders by status 'shipped'
Group by category to get total sales per category
Sort by total sales in descending order
Limit to top 3 categories
 
```js
db.orders.aggregate([
{ $match: { status: "shipped" } },
{ $group: { _id: "$category", totalSales: { $sum: "$sales" } } },
{ $sort: { totalSales: -1 } },
{ $limit: 3 }
]);
