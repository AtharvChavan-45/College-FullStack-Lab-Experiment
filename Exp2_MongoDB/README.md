# 📚 MongoDB CRUD Operations: Student Data

This repository documents the basic **Create, Read, Update, and Delete (CRUD)** operations performed using the `mongosh` interactive shell on a `students` collection within a `college` database.

---

## 💻 Environment & Setup Commands

These commands establish the context and environment in which the operations were performed.

| Command | Operation Performed | Explanation |
| :--- | :--- | :--- |
| `show dbs;` | Display Databases | Lists all databases on the MongoDB server. |
| `use college;` | Switch Database | Selects or creates the database named `college`. |
| `show collections;` | Display Collections | Confirms the existence of the `students` collection. |

---

## ➕ CREATE Operations (Insertion)

Documents were added using both single and multiple-document insertion methods.

| Command | Description | Command Code |
| :--- | :--- | :--- |
| **Insert One (Empty)** | Inserts a document with only the generated `_id`. | `db.students.insertOne({})` |
| **Insert One (Partial)** | Inserts a document with a single field (`name`). | `db.students.insertOne({name:"Atharv Chavan"})` |
| **Insert One (Full)** | Inserts a complete document. | `db.students.insertOne({name:"Atharv Chavan", age:21, branch:"CSE", year:3})` |
| **Insert Many** | Inserts an array of two documents in one call. | `db.students.insertMany([ {name:"Sneha Patil",...}, {name:"Amit Desai",...} ])` |

---

## 🔎 READ Operations (Querying)

Various queries were used to retrieve, filter, and project data.

| Command | Description | Command Code |
| :--- | :--- | :--- |
| **Find All** | Retrieves all documents in the collection. | `db.students.find()` |
| **Find One** | Retrieves the first document found. | `db.students.findOne()` |
| **Find by Filter** | Finds documents where the `age` field is `20`. | `db.students.find({age:20})` |
| **Find with Projection** | Retrieves all documents, but only displays the `name` and `age` fields (along with `_id`). | `db.students.find({},{name:1,age:1})` |
| **Pretty Format** | Displays the results in a readable JSON format. | `db.students.find().pretty()` |

---

## ✏️ UPDATE Operations (Modification)

Documents were modified using the **`$set`**, **`$inc`**, and **replacement** operators.

| Command | Operator Used | Description | Command Code |
| :--- | :--- | :--- | :--- |
| **`updateOne` with `$set`** | `$set` | Finds the first matching document and updates the `year` field to `4`. | `db.students.updateOne({name:"Atharv Chavan"}, {$set:{year:4}})` |
| **`updateMany` with `$inc`** | `$inc` | Finds all documents matching the criteria (`branch: "CSE"`) and increments the `age` field by `23`. | `db.students.updateMany({branch:"CSE"}, {$inc:{age:23}})` |
| **`replaceOne`** | (No operator) | Finds the first matching document and replaces its **entire content** (excluding `_id`) with the new document. | `db.students.replaceOne({name:"Amit Desai"}, {name:"Raj Jadhav",age:22,branch:"Mechnical",year:2})` |

---

## 🗑️ DELETE Operations (Removal)

Documents were removed using precise `_id` matching.

| Command | Description | Command Code |
| :--- | :--- | :--- |
| **Delete One by ID** | Deletes the single document matching the specific `_id` value. | `db.students.deleteOne({_id: ObjectId('69203ad9f4433f2456611ba5')})` |
| **Delete Many by ID** | Deletes documents matching the criteria. (Used here on a unique `_id` for demonstration). | `db.students.deleteMany({_id: ObjectId('69203b1df4433f2456611ba6')})` |

---

## 🚀 Advanced Operations for Revision

Here are additional essential MongoDB queries to consider for future work and revision.

| Command | Operation Performed | Explanation | Command Code |
| :--- | :--- | :--- | :--- |
| **Comparison Query** | Finds students where the `age` is **greater than** (`$gt`) 21. | Uses comparison operators for filtering. | `db.students.find({age: {$gt: 21}})` |
| **Sorting Results** | Retrieves all documents and sorts them by the `name` field in **ascending** (`1`) order. | Useful for displaying ordered results. | `db.students.find().sort({name: 1})` |
| **Bulk Deletion** | Deletes **all** documents that match the condition (`branch: "IT"`). | Efficient for cleaning up large sets of records. | `db.students.deleteMany({branch: "IT"})` |
| **Drop Collection** | **Permanently deletes** the entire `students` collection and all its data. | *Use with extreme caution!* | `db.students.drop()` |

### Count documents:

db.students.countDocuments()

### Sort:

db.students.find().sort({ age: 1 })

### Limit:

db.students.find().limit(2)

### Drop Collection:

db.students.drop()

### Drop Database:

db.dropDatabase()

---

## ✔ Summary
This README contains all important CRUD + basic mongo commands for fast revision and GitHub documentation.
