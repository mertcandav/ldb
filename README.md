# LDB: Local Database

LDB is a local, easy-to-use, thread-safe database for [Jule](https://github.com/julelang/jule).

It stores data as collections.\
Each collection has its own name for lookup.\
The data of the collections is basically a JSON array with query support.

Key features:
- Written in pure Jule
- Thread-safe
- Works on local and non-volatile memory
- Optimized for performance and memory efficiency balance
- Can store any JSON value, document oriented
- Simple CRUD and queries

## Quick Start Example

```rust
use "ldb"
use "std/fmt"

struct User {
	Name: str
	Age:  int
}

fn main() {
	// Open database, it will be created if not exist.
	mut db := Open("test.db")!
	defer { db.Close() }

	// Get users collection, it will be created if not exist.
	mut users := db.GetCollection[User]("users")!

	// Add sample data.
	users.Append(
		{"root", 0},
		{"admin", -1},
		{"Foo", 18},
		{"Bar", 28},
		{"Baz", 93},
		{"Fizz", 53},
		{"Buzz", 36},
		{"ABC", 36},
		{"XYZ", 18})!

	// Query for user count per age.
	// Result is: map[age]usercount
	userCounts := users.Query().
		Where(fn|user| user.Age > 0).
		GroupBy(fn|user| user.Age).
		Map(fn|group| group.Len())

	fmt::Println("user count per age: ", userCounts)
}
```

## Query Examples with SQL

```rust
// SELECT * FROM users
users.Query().Unwrap()
```
```rust
// SELECT * FROM users
// WHERE age > 25
users.Query().
	Where(fn|u| u.Age > 25).
	Unwrap()
```
```rust
// SELECT SUM(Price) FROM Products;
products.Query().
  Sum(fn|p| p.Price)
```
```rust
// SELECT * FROM Products
// ORDER BY Price DESC;
products.Query().
  OrderByDesc(fn|p| p.Price).
  Unwrap()
```
```rust
// INSERT INTO Customers (Name, ContactName, Address, City, PostalCode, Country)
// VALUES ('Cardinal', 'Tom B. Erichsen', 'Skagen 21', 'Stavanger', '4006', 'Norway');
customers.Append({
	Name: "Cardinal",
	ContactName: "Tom B. Erichsen",
	Address: "Skagen 21",
	City: "Stavanger",
	PostalCode: "4006",
	Country: "Norway",
})!
```
```rust
// DELETE FROM Customers WHERE Name = 'Alfreds Futterkiste';
customers.Delete(fn|c| c.Name == "Alfreds Futterkiste")!
```
```rust
// UPDATE Customers
// SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
// WHERE ID = 1;
customers.Update(fn|mut c| {
	if c.ID == 1 {
		c.ContactName = "Alfred Schmidt"
		c.City = "Frankfurt"
	}
})!
```

## License

LDB is distributed under the terms of the BSD 3-Clause license. <br>
[See License Details](./LICENSE)
