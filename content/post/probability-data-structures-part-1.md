---
title: "Probabilistic Data Structures Part - 1"
date: 2019-04-18
publishdate: 2019-04-18
lastmod: 2019-04-18
draft: false
tags: ["probabilistic data structures", "algorithms", "data structures", "bloom filters", "go", "golang"]
---

Suppose let's say you are signing up for the new Email id. When you enter a new mail id,respective mail service will check whether the given mail id already exists.

![image alt text](/img/email.png)

So how can we build such a kind of systems ?

No, we cannot keep all email id's in memory because it's not practical. We can store all email id's on disk and we can read from the disk and check whether the given mail id exists or not, but reading and writing to disk is time consuming. We can do better, We can use Probabilistic Data Structure to solve this problem.

### Bloom Filter

It is a space-efficient probabilistic data structure for representing a set D = {x1, x2,..., xn} of n elements that supports only two operations.

- Adding an elements to the set {Add}
- Checking whether the element exist in the set or not {Contains}

When bloom filter checks whether an item exist in the set, it can give either it's present or not present. If the bloom filter gives the result as not present, it's 100% sure the item does not exist in the set but it is not same for when the result is present, because when the result is present, it's only 90% sure that the item exist.

- **Contains(item) => "May be in the set" or "Definitely not in set"**
- **Definitely not in set is 100%**

Hashing plays the central role in probabilistic data structures as they use it for randomization and compact representation of the data.

So, what is hashing ?

**Hashing is a function which takes an inputs of varying size and generates fixed size value which is called hash value.**

There are many hash functions available, the choice of hash function are important to avoid bias. When a hash function compresses the input, it may generate same hash value for two or more different inputs which is unavoidable and this is called hash collision.
Probablistic data strctures uses non Cryptographic hash functions. The reason for the use of non-cryptographic hash function is that they're significantly faster than cryptographic hash functions.

The bloom filter is represented by a bit array and can be described by its length **L** and number of different hash functions **H**. Each hash function should be independent and uniformly distributed. In this way, we randomize the hash values uniformly (you can think of it as using hash functions as some kind of random-number generator) in the filter and decrease the probability of hash collisions. Such an approach drastically reduces the storage space and, regardless of the number of elements in the data structure and their size, requires a constant number of bits by reserving a few bits per element.

In the begining the bit array of length **L** all bits are equal to zero. i.e. Set is empty.

![image alt text](/img/bit_array1.png)

To insert an element **x** into the filter, for every hash function **H(i)** we compute its value **j** = **H(i){x}** on the element **x** and set the corresponding bit **j** in the filter to one. Note, it is possible that some bits can be set multiple times due to hash collisions.

![image alt text](/img/bit_array2.png)

In the above diagram, I've used only two hash function, but in practice, we will use more hash functions. It is possible that different elements can share corresponding bits(both inputs shared 5th index in the above pic)

When we check for the element **x** in the filter, we compute all hash functions **H(i){x}** for i=1,2,...n and check bits in the corresponding positions. If all bits are set to one, then the element **x** may exist in the filter. Otherwise, the element **x** is definitely not in the filter.

![image alt text](/img/bit_array3.png)

In the above case, for the input "justincampell" we compute all hash functions in this case we have two hash functions and that gives us two indices(2, 8). Thus, checking the bits 2 and 8, we see that bit 2 isn’t set, therefore the item "justincampell" is ***definitely not in the set*** and we don’t even need to check bit 8.

![image alt text](/img/bit_array4.png)

Similarly, For the above case we pass the input "jack.cbe" to our hash functions which results indices(5, 23). After that, we check the corresponding bits in the set and see that both of them are set to one, therefore we claim that "jack.cbe" ***may exist in the set***.

### Code

- Let's define bloom filter struct in go

```go
// BloomFilter struct definition
type BloomFilter struct {
    bitArray      []bool        // BloomFilter bit array
    bitArraySize  int           // Number of bits to use to persist elements
    maxSize       int           // Maximum Size of the bloom filter
    hashFunctions []hash.Hash64 // List of hash functions
}
```

- Adding an element to the set

```go
// Add adding an elements to the filter
func (bl *BloomFilter) Add(item []byte) {
    indices := bl.fetchIndices(item)
    for _, index := range indices {
        bl.bitArray[index] = true
    }
}
```

- Checking whether the element exist in the set or not

```go
// Contains checking whether the element exist in the set or not
func (bl *BloomFilter) Contains(item []byte) bool {
    indices := bl.fetchIndices(item)
    for _, index := range indices {
        bitValue := bl.bitArray[index]
        if !bitValue {
            return false
        }
    }
    return true
}
```

Complete code in [github](https://gist.github.com/khekrn/ccee44c93e614593efc19a5b46ef95d0)

### Use Cases

- Google Bigtable, Apache HBase and Apache Cassandra, Postgresql and many more databases use Probabilistic data structures to reduce the disk lookups for non-existent rows or columns. Avoiding costly disk lookups considerably increases the performance of a database query operation.
- Medium uses Probabilistic data structures to check if an article has already been recommended to an user.
- Ethereum uses Probabilistic data structures for quickly finding logs on the Ethereum blockchain.
- URL Shortners
- The Google Chrome web browser used to use a Probabilistic data structure to identify malicious URLs.
- For realtime monitoring dashboards and many more.

### Conclusion

- Probabilistic data structure is a data structure which always provides approximated answers, but with reliable ways to estimate possible errors.
- False positive is possible but false negatives are not. False Positive, Item does not exist but bloom filter says it does. False Negative, Item do exist but bloom filter says it does not.

### References

- [How many optimal hash functions required](https://stackoverflow.com/questions/658439/how-many-hash-functions-does-my-bloom-filter-need)
- [Probabilistic Data Structures](https://www.youtube.com/watch?v=-tT40zeWgXM&t=294s)
- [Andrii Gakhov - Probabilistic Data Structures](https://www.gakhov.com/)