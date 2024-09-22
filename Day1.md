1. $match (Filters documents)
Purpose: Filters the documents to match certain criteria.
Real-time Use: Filtering orders by status (e.g., only shipped orders).
js
Copy code
db.orders.aggregate([
  { $match: { status: "shipped" } }
]);
Result: Returns documents where the status is shipped.
2. $group (Groups data by a field)
Purpose: Groups documents by a field and applies aggregate functions like sum, avg, count, etc.
Real-time Use: Calculate total sales and number of items sold for each product.
js
Copy code
db.orders.aggregate([
  { $group: { _id: "$product", totalSales: { $sum: "$sales" }, totalItems: { $sum: "$quantity" } } }
]);
Result: For each product, it returns total sales and quantity sold.
3. $project (Selects specific fields)
Purpose: Allows you to include, exclude, or rename fields.
Real-time Use: Exclude some sensitive fields from results, such as hiding customer emails.
js
Copy code
db.customers.aggregate([
  { $project: { name: 1, email: 0 } }  // Includes only the name, excludes the email
]);
Result: Shows only the names, hiding email addresses.
4. $lookup (Performs a join with another collection)
Purpose: Similar to SQL joins, it allows you to merge documents from another collection.
Real-time Use: Retrieve customer details for each order.
js
Copy code
db.orders.aggregate([
  { $lookup: {
      from: "customers", 
      localField: "_id", 
      foreignField: "order_id", 
      as: "customerDetails"
    }
  }
]);
Result: Joins orders with customers collection based on the order ID.
5. $sort (Sorts documents)
Purpose: Sorts the documents by a specific field.
Real-time Use: Sort orders by total sales in descending order.
js
Copy code
db.orders.aggregate([
  { $sort: { sales: -1 } }  // -1 for descending, 1 for ascending
]);
Result: Orders sorted by highest sales.
6. $unwind (Deconstructs an array field)
Purpose: Splits array fields into separate documents.
Real-time Use: If an order contains multiple items in an array, each item is transformed into its own document.
js
Copy code
db.orders.aggregate([
  { $unwind: "$items" }  // Assuming each order has an array "items"
]);
Result: Each item in an array gets its own document.
7. $limit and $skip (Limit and skip documents)
Purpose: Limits or skips a certain number of documents.
Real-time Use: Use it for pagination when fetching data in a web app.
js
Copy code
db.orders.aggregate([
  { $sort: { orderDate: -1 } },
  { $skip: 10 },  // Skip the first 10 documents
  { $limit: 5 }   // Then return the next 5 documents
]);
Result: Useful for pagination, retrieves the 11th to 15th orders sorted by date.
8. $addFields (Adds new fields to documents)
Purpose: Adds computed or derived fields to the documents.
Real-time Use: Add a field for calculating sales tax.
js
Copy code
db.orders.aggregate([
  { $addFields: { salesTax: { $multiply: ["$sales", 0.08] } } }  // Assuming 8% tax rate
]);
Result: Each document now has a salesTax field.



Memory updated
Step-by-Step Guide to MongoDB Aggregation Framework
The MongoDB Aggregation Framework allows you to perform operations on data in a collection, similar to SQL's GROUP BY, JOIN, SUM, and other functions. It processes data records and returns computed results. The framework works by creating a pipeline of stages that transform the documents.

Here’s a breakdown of key aggregation stages and their real-time use cases:

1. $match (Filters documents)
Purpose: Filters the documents to match certain criteria.
Real-time Use: Filtering orders by status (e.g., only shipped orders).
js
Copy code
db.orders.aggregate([
  { $match: { status: "shipped" } }
]);
Result: Returns documents where the status is shipped.
2. $group (Groups data by a field)
Purpose: Groups documents by a field and applies aggregate functions like sum, avg, count, etc.
Real-time Use: Calculate total sales and number of items sold for each product.
js
Copy code
db.orders.aggregate([
  { $group: { _id: "$product", totalSales: { $sum: "$sales" }, totalItems: { $sum: "$quantity" } } }
]);
Result: For each product, it returns total sales and quantity sold.
3. $project (Selects specific fields)
Purpose: Allows you to include, exclude, or rename fields.
Real-time Use: Exclude some sensitive fields from results, such as hiding customer emails.
js
Copy code
db.customers.aggregate([
  { $project: { name: 1, email: 0 } }  // Includes only the name, excludes the email
]);
Result: Shows only the names, hiding email addresses.
4. $lookup (Performs a join with another collection)
Purpose: Similar to SQL joins, it allows you to merge documents from another collection.
Real-time Use: Retrieve customer details for each order.
js
Copy code
db.orders.aggregate([
  { $lookup: {
      from: "customers", 
      localField: "_id", 
      foreignField: "order_id", 
      as: "customerDetails"
    }
  }
]);
Result: Joins orders with customers collection based on the order ID.
5. $sort (Sorts documents)
Purpose: Sorts the documents by a specific field.
Real-time Use: Sort orders by total sales in descending order.
js
Copy code
db.orders.aggregate([
  { $sort: { sales: -1 } }  // -1 for descending, 1 for ascending
]);
Result: Orders sorted by highest sales.
6. $unwind (Deconstructs an array field)
Purpose: Splits array fields into separate documents.
Real-time Use: If an order contains multiple items in an array, each item is transformed into its own document.
js
Copy code
db.orders.aggregate([
  { $unwind: "$items" }  // Assuming each order has an array "items"
]);
Result: Each item in an array gets its own document.
7. $limit and $skip (Limit and skip documents)
Purpose: Limits or skips a certain number of documents.
Real-time Use: Use it for pagination when fetching data in a web app.
js
Copy code
db.orders.aggregate([
  { $sort: { orderDate: -1 } },
  { $skip: 10 },  // Skip the first 10 documents
  { $limit: 5 }   // Then return the next 5 documents
]);
Result: Useful for pagination, retrieves the 11th to 15th orders sorted by date.
8. $addFields (Adds new fields to documents)
Purpose: Adds computed or derived fields to the documents.
Real-time Use: Add a field for calculating sales tax.
js
Copy code
db.orders.aggregate([
  { $addFields: { salesTax: { $multiply: ["$sales", 0.08] } } }  // Assuming 8% tax rate
]);
Result: Each document now has a salesTax field.
Real-Time Use: Sharded Aggregation Operations
Sharding splits large collections across multiple MongoDB instances for scalability. When you perform an aggregation on a sharded collection:

Purpose: MongoDB routes the aggregation to different shards, processes the data on each shard, and then combines the results.
Example: You can run the same aggregation queries like $group and $match on a sharded collection. MongoDB automatically handles distributing data across shards and merging the results.

Ensure your collection is sharded:

js
Copy code
sh.enableSharding("myDatabase");
sh.shardCollection("myDatabase.orders", { "category": "hashed" });
Then, run your aggregation queries just like on a non-sharded collection:

js
Copy code
db.orders.aggregate([
  { $match: { status: "shipped" } },
  { $group: { _id: "$product", totalSales: { $sum: "$sales" } } }
]);
Additional Practice Aggregation Pipeline
Here’s a combined pipeline using multiple stages:

Filter orders by status 'shipped'
Group by category to get total sales per category
Sort by total sales in descending order
Limit to top 3 categories
js
Copy code
db.orders.aggregate([
  { $match: { status: "shipped" } },
  { $group: { _id: "$category", totalSales: { $sum: "$sales" } } },
  { $sort: { totalSales: -1 } },
  { $limit: 3 }
]);