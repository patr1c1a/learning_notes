# "DB Indexing in System Design Interviews - B-tree, Geospatial, Inverted Index, and more!"

**Video citation:** DB Indexing in System Design Interviews - B-tree, Geospatial, Inverted Index, and more!. <www.youtube.com>. Retrieved March 10, 2025, from [https://www.youtube.com/watch?v=BHCSL_ZifI0](https://www.youtube.com/watch?v=BHCSL_ZifI0)

---

## What is an index

Pages: organize data in databases. Usually 8kb per page.

If no indexing exists, the database has to read all the pages to get the information, pulling them from disk into memory one by one (one trip from SSD to RAM it takes about 100 micro seconds).

An index is a data structure that tells us in which pages the information is, so we can read it in one trip. So we first pull the index into memory, find the page, and then pull the page to find the whole information.

## B-trees

It's the most popular database index. Each node in the tree is a sorted list of values with left and right pointers, either to another node or to a data page in disk.

Example query:

```sql
SELECT *
FROM users
WHERE age = 51
```

And let's assume an index on age.

![B-tree example with a root containing values 20, 50, 90 and 4 nodes, where one of them contains values 55, 67 and points to Page 3](https://www.plantuml.com/plantuml/dpng/ZLJ1ZjCm5BpxAvwuS8f4cosmbsgHE73Wm9tbuAqdYI5vZkoNXWZjlzFQqIPTkUggKYkzcVJDl1db8OZsCdIjUazVKLa6IGFopWa5zDfhGJkHbZWMrojnDQI1ft-AYCZZ89hh5lJkzD4XFPgmL_ZsGoJ829OfHzWbGcMhF7wVSBQCcV2idjL9-NVe8oYu3XHwVG1fDcGMEm47TvgqEAAzQO2TmIRlO3VhEoA7VeueRDr83jxCzi6pzElhj_drPTelZVKo9HKt_gYyucEt7FgQcu-V8YVOlp857D-W-Yw_x7wpIiqnyhT9nsa6DXucJzb6YcFCB1dTKeBAHZ7qo3zq3IeVcIX9wtMDyjoIBzwxCT0JP0IOQk-6FgXKHogC6xdOf_HLlCp5R9kPbIgPkXZUgGXHKUoNrT-iCM3IIO4u0bVEdulQsfexiD1efR5CbTSTWZgdGiNF9ZxtQp-tAdwlnh5CIntMo39Dmb_XwrnVabdXBqdCloleCe5uMFDx2EaDqUh93P8SXi5ZiJmSciWO-XwUdfmty2hyiTphasLgeuWUvBmqJZs0pT2r_m40)

We would first pull the root node into memory. Since 51 is larger than 50 but smaller than 90, we go to the right child of the root. The right child contains value 55, which is larger than 51 and points to Page 3, so we go to the page and find the information.

If the query was:

```sql
SELECT *
FROM users
WHERE age > 51
```

we would pull both nodes that are bigger than 51 into memory and follow all of their pointers to find all values that are greater than 51.

## Hash index

The information goes through a hash function that converts it into a hash. Then we have a key-value pair where the key is the hash and the value is a pointer to the page.

They offer O(1) lookups but don't support range queries. And B-trees have a similar performance, so hash indexes are usually not used for databases, but mostly for in-memory solutions (like Redis and other caching solutions).

## Geospatial

Useful when dealing with two dimensional data. E.g.: when trying to search within regions on latitude and longitude (like "find people within a given radius"). B-trees are only good for one dimensional data.

Example:

```sql
SELECT *
FROM location
WHERE lat > 100 AND lat < 400
  AND long > 20 AND long < 200
```

In this case we would need to pull a whole "strip" of data for latitude and another whole strip for longitude and then merge them until we get their intersection. Three most popular geospatial indexing algorithms:

- Geohashing: takes a map and divides it into 4 squares, then recursively splits each one of those cells and does the same, getting increasing levels of precision. Then creates geohashes and builds a B-tree on top of those hashes, allowing range queries with nearly O(1) performance in precise areas. Geohashing is very popular.
- Quadtrees: also splits the map recursively, mapping the splitting to a tree and adding more levels to tree only for those places where there's a higher density. This tree is the index. Quadtrees are not used in production so much.
- R-trees: they're similar to quadtrees, but instead of splitting in even portions, they do some clustering for data that is close to each other, with the ability to group and even overlap some of those clusters. There's also a tree, where more levels are added for increasing precision (deeper levels in the tree mean going recursively deeper in the map clusters). R-trees are preferred to quadtrees in production.

## Inverted index

Suited for whenever you need to search for text. B-trees are sorted, so they're not good for queries like this:

```sql
SELECT *
FROM document
WHERE name like '%fast%'
```

A B-tree would be good for a prefix search ('fast%') but not this one where we need to find a substring in any part of our text.

An inverted index maps tokens to the places where they can be found. E.g.: let's suppose we have 3 documents with the following contents:

```txt
doc1: "B-trees are fast and reliable"
doc2: "Hash tables are fast but limited"
doc3: "B-trees handle range queries well"
```

And we create a hash map where the key is a token and the value is a list of all the documents that contain that token (or pointers to those documents):

```txt
b-trees -> [doc1, doc3]
fast -> [doc1, doc2]
reliable -> [doc1]
hash -> [doc2]
tables -> [doc2]
limited -> [doc2]
handle -> [doc3]
range -> [doc3]
queries -> [doc3]
```

Inverted indexes are used in elastic search and anytime you're doing a full text search.

## Conclusion

Indexes are important in order to know where our queries might be inefficient and to which columns we should apply indexes.

![Flow chart](https://www.plantuml.com/plantuml/dpng/ZTBFJeGm40RmUvzYZhk7JVIgYMvC_BE9yU8cnkDOFcnZQR4TNF3fBNJ30QDP3b3ey9kF2Tia7ALhd5BJIYbRq-e9g0XrRRM55wfOc5Xhf7Iz9Z7mkIIi5UNZugvpZlRyva3Fcltbz3H9Q1LS0grUaKhb29UwPBz1LtH-za4n77wRVxkpFIuM_hHtiA8DhLuC2ydGWa9zZ1z1G-ZeiqCSh7_FJUUtD8yGJHq5lL02nymKTqwqyr-8Wefsla8_1vf3BRt7e5biy2N00hj7I6tUPdSgj_ER1as80w5dBTImQ8EqX1yucLF9s_n1WvWyZZBC1NMpsKVW7mQ-AXDNnpjRgt9MstmTVwiV)
