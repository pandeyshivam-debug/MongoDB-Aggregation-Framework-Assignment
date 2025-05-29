# HARD

### 1. Category Value and Classification:
```
db.products.aggregate([
  {
    $group: {
      _id: "$category",
      totalInventoryValue: {
        $sum: { $multiply: ["$price", "$quantity"] }
      }
    }
  },
  {
    $project: {
      _id: 0,
      categoryName: "$_id",
      totalInventoryValue: 1,
      valueClassification: {
        $switch: {
          branches: [
            { case: { $gt: ["$totalInventoryValue", 10000] }, then: "High Value" },
            { case: { $gt: ["$totalInventoryValue", 5000] }, then: "Medium Value" }
          ],
          default: "Standard Value"
        }
      }
    }
  }
]);
```
#### (part of output)
![alt text](image.png)

### 2. Suppliers and Their Most Expensive Product:
```
db.products.aggregate([
  {
    $sort: {
      "supplier.name": 1,
      price: -1
    }
  },
  {
    $group: {
      _id: "$supplier.name",
      mostExpensiveProductName: { $first: "$name" },
      maxPrice: { $first: "$price" }
    }
  },
  {
    $project: {
      _id: 0,
      supplierName: "$_id",
      mostExpensiveProductName: 1,
      maxPrice: 1
    }
  }
])
```
![alt text](image-1.png)

### 3. Products with "portable" tag but NOT "computer" tag:
```
db.products.aggregate([
  {
    $match: {
      tags: {
        $all: ["portable"],
        $nin: ["computer"]  
      }
    }
  },
  {
    $project: {
      _id: 0,
      name: 1,
      tags: 1
    }
  }
]);
```
![alt text](image-2.png)