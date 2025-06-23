## Index

[fn Open\(path: str\)\!: &amp;LDB](#open)\
[struct Query](#query)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Cache\(self\): Query\[T\]](#cache)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Sum\[E: numeric\]\(self, f: fn\(T\): E\): \(r: E\)](#sum)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Count\(self, f: fn\(T\): bool\): \(n: int\)](#count)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Contains\(self, f: fn\(T\): bool\): \(r: bool\)](#contains)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Where\(self, f: fn\(T\): bool\): Query\[T\]](#where)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn OrderBy\[E: comparable\]\(self, f: fn\(T\): E\): Query\[T\]](#orderby)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn OrderByDesc\[E: comparable\]\(self, f: fn\(T\): E\): Query\[T\]](#orderbydesc)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn GroupBy\[E: comparable\]\(self, f: fn\(T\): E\): GroupByQuery\[E, T\]](#groupby)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Reverse\(self\): Query\[T\]](#reverse)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Unwrap\(self\): \[\]T](#unwrap)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Final\(self\)](#final)\
[struct GroupByQuery](#groupbyquery)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Len\(self\): int](#len)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Max\[N: numeric\]\(self, f: fn\(T\): N\): map\[E\]N](#max)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Min\[N: numeric\]\(self, f: fn\(T\): N\): map\[E\]N](#min)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Sum\[N: numeric\]\(self, f: fn\(T\): N\): map\[E\]N](#sum-1)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Avg\[N: numeric\]\(self, f: fn\(T\): N\): map\[E\]N](#avg)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Map\[V\]\(self, f: fn\(Group\[T\]\): V\): map\[E\]V](#map)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Having\(self, f: fn\(Group\[T\]\): bool\): GroupByQuery\[E, T\]](#having)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Unwrap\(self\): map\[E\]\[\]T](#unwrap-1)\
[struct Group](#group)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Len\(self\): int](#len-1)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Max\[N: numeric\]\(self, f: fn\(T\): N\): \(r: N\)](#max-1)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Min\[N: numeric\]\(self, f: fn\(T\): N\): \(r: N\)](#min-1)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Sum\[N: numeric\]\(self, f: fn\(T\): N\): \(r: N\)](#sum-2)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Avg\[N: numeric\]\(self, f: fn\(T\): N\): \(r: N\)](#avg-1)\
[struct LDB](#ldb)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn IsOpen\(&amp;self\): bool](#isopen)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn GetCollection\[T\]\(mut &amp;self, name: str\)\!: &amp;Collection\[T\]](#getcollection)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn DropCollection\(mut &amp;self, name: str\)\!](#dropcollection)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn ClearCollections\(mut &amp;self\)\!](#clearcollections)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Collections\(&amp;self\)\!: \[\]str](#collections)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Close\(&amp;self\)](#close)\
[struct Collection](#collection)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Name\(&amp;self\): str](#name)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Len\(&amp;self\)\!: \(n: int\)](#len-2)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Iter\(mut &amp;self, f: fn\(mut T\)\)\!](#iter)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Iter2\(mut &amp;self, f: fn\(mut T\): bool\)\!](#iter2)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Append\(mut &amp;self, mut values: \.\.\.T\)\!](#append)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Map\(mut &amp;self, f: fn\(T\): T\)\!](#map-1)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Update\(mut &amp;self, f: fn\(mut &amp;t: T\)\)\!](#update)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Delete\(mut &amp;self, f: fn\(T\): bool\)\!](#delete)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn ClearCache\(mut &amp;self\)](#clearcache)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Query\(mut &amp;self\): Query\[T\]](#query-1)\
&nbsp;&nbsp;&nbsp;&nbsp;[fn Close\(mut &amp;self\)](#close-1)



## Open
```jule
fn Open(path: str)!: &LDB
```
Opens new LDB by path\. If database is not exist, will be created\.

## Query
```jule
struct Query[T] {
	// NOTE: contains filtered hidden or unexported fields
}
```
LDB collection query\.

A Query instance must not be copied\. Should be terminated after use with read\-unlocker operation\. Otherwise, bevahior is undefined\. Basically, if a Query method returns a Query instance, read\-lock will not be released\. Otherwise, if method returns meaningful result, unwraps Query, read\-lock will be released\.

The query result will be immutable copy of the database data\. Mutating any data will not be reflected\.

Example to proper use:<br>
```
collection.Query().
	Where(fn|fbz| fbz.Foo > 500).
	OrderBy(fn|fbz| fbz.Foo).
	Unwrap()
```


### Cache
```jule
fn Cache(self): Query[T]
```
Caches data and switches to it\. Query will drop the collection data and use cache for subsequent queries\. It may increase performance of your query, but overuse may cause high memory usage\. Query will cache the data automatically, if needed\. Mostly, this function should not be invoked manually\.

Since caching drops the collection data, trying to cache already cached query instance may result as empty cache\. Behavior is undefined for repetetive use\.

### Sum
```jule
fn Sum[E: numeric](self, f: fn(T): E): (r: E)
```
Returns total value of the results of f\. The start value is zero\. Unlocks the read\-lock\.

### Count
```jule
fn Count(self, f: fn(T): bool): (n: int)
```
Counts values by condition\. To count a value, f must report true for it\. Unlocks the read\-lock\.

### Contains
```jule
fn Contains(self, f: fn(T): bool): (r: bool)
```
Reports whether data includes at least one value reported true by f\. Unlocks the read\-lock\.

### Where
```jule
fn Where(self, f: fn(T): bool): Query[T]
```
Filters data by result of f\.

### OrderBy
```jule
fn OrderBy[E: comparable](self, f: fn(T): E): Query[T]
```
Orders data by result data of f\. Orders by ascending\.

### OrderByDesc
```jule
fn OrderByDesc[E: comparable](self, f: fn(T): E): Query[T]
```
Orders data by result data of f\. Orders by descending\.

### GroupBy
```jule
fn GroupBy[E: comparable](self, f: fn(T): E): GroupByQuery[E, T]
```


### Reverse
```jule
fn Reverse(self): Query[T]
```
Reverses data\.

### Unwrap
```jule
fn Unwrap(self): []T
```
Returns query result as slice\. Unlocks the read\-lock\.

### Final
```jule
fn Final(self)
```
Final stage of the Query with no result\. Returns nothing\. Unlocks the read\-lock\.

## GroupByQuery
```jule
struct GroupByQuery[E: comparable, T] {
	// NOTE: contains filtered hidden or unexported fields
}
```
LDB collection query\.

A GroupByQuery instance must not be copied\. Should be terminated after use\. Otherwise, bevahior is undefined\.

The query result will be immutable copy of the database data\. Mutating any data will not be reflected\.

Example to proper use:<br>
```
collection.Query().
	Where(fn|fbz| fbz.Foo > 500).
	OrderBy(fn|fbz| fbz.Foo).
	Unwrap()
```


### Len
```jule
fn Len(self): int
```
Returns number of groups\.

### Max
```jule
fn Max[N: numeric](self, f: fn(T): N): map[E]N
```
Returns maximum value by result of f\.

### Min
```jule
fn Min[N: numeric](self, f: fn(T): N): map[E]N
```
Returns minimum value by result of f\.

### Sum
```jule
fn Sum[N: numeric](self, f: fn(T): N): map[E]N
```
Returns total value of the results of f\. The start value is zero\.

### Avg
```jule
fn Avg[N: numeric](self, f: fn(T): N): map[E]N
```
Returns average value of the results of f\. The start value is zero to sum\. Divides total value by length of the group\.

### Map
```jule
fn Map[V](self, f: fn(Group[T]): V): map[E]V
```
Maps group data to result of f\.

### Having
```jule
fn Having(self, f: fn(Group[T]): bool): GroupByQuery[E, T]
```
Filters groups by result of f\.

### Unwrap
```jule
fn Unwrap(self): map[E][]T
```
Returns query result as map\.

## Group
```jule
struct Group[T] {
	// NOTE: contains filtered hidden or unexported fields
}
```
A GroupByQuery group\.

A Group instance must not be copied\. Should be terminated after use\. Otherwise, bevahior is undefined\.

Intended to in\-query use in GroupByQuery queries\.

### Len
```jule
fn Len(self): int
```
Returns number of values in the group\.

### Max
```jule
fn Max[N: numeric](self, f: fn(T): N): (r: N)
```
Returns maximum value by result of f\.

### Min
```jule
fn Min[N: numeric](self, f: fn(T): N): (r: N)
```
Returns minimum value by result of f\.

### Sum
```jule
fn Sum[N: numeric](self, f: fn(T): N): (r: N)
```
Returns total value of the results of f\. The start value is zero\.

### Avg
```jule
fn Avg[N: numeric](self, f: fn(T): N): (r: N)
```
Returns average value of the results of f\. The start value is zero to sum\. Divides total value by length of the group\.

## LDB
```jule
struct LDB {
	// NOTE: contains filtered hidden or unexported fields
}
```
Local Database instance\. A LDB instance must not be copied\.

### IsOpen
```jule
fn IsOpen(&self): bool
```
Reports whether the database connection is open\.

### GetCollection
```jule
fn GetCollection[T](mut &self, name: str)!: &Collection[T]
```
Returns collection from the database\. If collection is not exist in the specified name, it will be created\. Type T must be supported by the standard JSON package\.

### DropCollection
```jule
fn DropCollection(mut &self, name: str)!
```
Removes collection from the database\. If collection is not exist in the specified name, does nothing\. If any collection linked to the name currently, it will fail\. Close the collection connection before drop it\.

### ClearCollections
```jule
fn ClearCollections(mut &self)!
```
Removes all collections from the database\. If any collection linked currently, it will fail\. Close the collection connections before drop them\.

### Collections
```jule
fn Collections(&self)!: []str
```
Returns names of collections\.

### Close
```jule
fn Close(&self)
```
Closes the database connection\. If connection is already closed, it does nothing\.

## Collection
```jule
struct Collection[T] {
	// NOTE: contains filtered hidden or unexported fields
}
```
LDB collection instance\. A Collection instance must not be copied\.

### Name
```jule
fn Name(&self): str
```
Returns name of the collection\.

### Len
```jule
fn Len(&self)!: (n: int)
```
Returns number of records in the collection\.

### Iter
```jule
fn Iter(mut &self, f: fn(mut T))!
```
Iterates values of collection\.

### Iter2
```jule
fn Iter2(mut &self, f: fn(mut T): bool)!
```
Iterates values of collection\. Breaks iteration when function f returns false\.

### Append
```jule
fn Append(mut &self, mut values: ...T)!
```
Appends values to collection\.

### Map
```jule
fn Map(mut &self, f: fn(T): T)!
```
Maps data to result of f\.

### Update
```jule
fn Update(mut &self, f: fn(mut &t: T))!
```
Updates data by f\. The function f must filter data if needed and update the mutable reference\.

### Delete
```jule
fn Delete(mut &self, f: fn(T): bool)!
```
Filters and removes matched data by f\.

### ClearCache
```jule
fn ClearCache(mut &self)
```
Clears internal cache\. It may help to reduce memory use\. But triggers caching and reading from non\-volatile memory in next CRUD\. Unnecessary use of this method may cause significant performance issues\.

### Query
```jule
fn Query(mut &self): Query[T]
```
Returns ready\-to\-use query for the collection\. Panics if database\-read fails\. Locks the read\-lock, avoid misuse of Query, otherwise behavior is undefined\.

### Close
```jule
fn Close(mut &self)
```
Drops the collection ownership\. When a collection closed, all collections will be closed for the same name\.