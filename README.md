# Conditions-Variables-In-MongoDB

### Why conditions in MongoDb?

#### MongoDB uses a JSON-like syntax for conditions, primarily used in the find method to filter documents based on criteria. Conditions allow you to query documents that meet specific requirements. They are essential for finding, updating, or deleting data selectively.

### 1) `gt`

#### Find documents where the "age" field is greater than 30

```
db.users.find({ age: { $gt: 30 } });
```

### 2) `find`

#### Find documents where "name" is "John" and "age" is 25

```
db.users.find({ name: "John", age: 25 });
```

### 3) `in`

#### Find documents where "status" is either "active" or "pending"

```
db.users.find({ status: { $in: ["active", "pending"] } });
```

### 4) `in`

#### Matches documents where the field is equal to a specified value.

```
db.users.find({ age: { $eq: 25 } });
```

### 5) `cond`

#### The $cond operator is similar to an if-else statement, allowing you to add conditions for transformations and computations.

```
db.sales.aggregate([
  {
    $project: {
      status: {
        $cond: {
          if: { $gte: ["$total", 100] },
          then: "High",
          else: "Low"
        }
      }
    }
  }
]);
```

### 6) `cond`

#### In MongoDB, the $switch statement is used within aggregation pipelines to evaluate a series of conditions and return a result based on the first condition that evaluates to true. It functions similarly to a switch or case statement found in other programming languages. The $switch operator is ideal for handling multiple conditional checks in a clean and readable way. When you have more than two conditions to evaluate, $switch can simplify the logic compared to using multiple $cond statements.

```
db.products.aggregate([
  {
    $project: {
      name: 1,
      priceCategory: {
        $switch: {
          branches: [
            { case: { $lt: ["$price", 50] }, then: "Low" },
            { case: { $and: [{ $gte: ["$price", 50] }, { $lt: ["$price", 200] }] }, then: "Medium" },
            { case: { $gte: ["$price", 200] }, then: "High" }
          ],
          default: "Unknown"
        }
      }
    }
  }
]);
```

### Why varables?

#### Variables in MongoDB are used within aggregation pipelines to store intermediate values or handle data transformations. Variables enable complex data manipulations and calculations inside an aggregation pipeline, making it easier to pass values between stages.

### 1) `let`

#### Using $let to define a variable:

```
db.sales.aggregate([
  {
    $project: {
      totalRevenue: {
        $let: {
          vars: {
            unitPrice: "$price",
            unitsSold: "$quantity"
          },
          in: { $multiply: ["$$unitPrice", "$$unitsSold"] }
        }
      }
    }
  }
]);
```

### 2) `NOW`

#### This system variable is used to find current date of our system.

```
db.collection.aggregate({$eq:["$price","100"]})
```

### 3) `System Variables`

```
$$NOW: Represents the current date and time. Useful for comparing date fields to the current time.
$$ROOT: References the entire document at the current stage of the pipeline.
$$CURRENT: Refers to the current document being processed in the pipeline stage. Similar to $$ROOT, but differs when used in nested stages.
$$REMOVE: Used to indicate that a field should be removed from a document.
$$DESCEND: Applies to expressions that traverse arrays, allowing you to process nested array fields.
```

### 4) `User Variables`

- User variable names can contain the ascii characters [_a-zA-Z0-9] and any non-ascii character.
- User variable names must begin with a lowercase ascii letter [a-z] or a non-ascii character.
