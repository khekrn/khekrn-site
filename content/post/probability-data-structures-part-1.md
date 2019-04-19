---
title: "Probabilistic Data Structures Part - 1"
date: 2019-04-18
publishdate: 2019-04-18
lastmod: 2019-04-18
draft: false
tags: ["go", "golang", "probability", "algorithms", "data-structures"]
---

## Introduction

Probabilistic data structure is a data structure which always provides approximated answers, but with reliable ways to estimate possible errors. Basically, it stores a summary of data instead of storing the entire data that's why it gives us approximate results instead of deterministic results. The errors are compensated with low memory requirements, constant query/lookup time, scaling and many other factors which are essential in distributed data applications.

## Use Cases

- Google Bigtable, Apache HBase and Apache Cassandra, Postgresql and many more databases use Probabilistic data structures to reduce the disk lookups for non-existent rows or columns. Avoiding costly disk lookups considerably increases the performance of a database query operation.
- Medium uses Probabilistic data structures to check if an article has already been recommended to an user.
- Ethereum uses Probabilistic data structures for quickly finding logs on the Ethereum blockchain.
- The Google Chrome web browser used to use a Probabilistic data structure to identify malicious URLs.
- For realtime monitoring dashboards and many more.

## Hashing

Hashing plays the central role in probabilistic data structures as they use it for randomization and compact representation of the data.

So, what is hashing ?

**Hashing is a function which takes an inputs of varying size and generates fixed size value which is called hash value.**

There are many hash functions available, the choice of hash function are important to avoid bias. When a hash function compresses the input, it may generate same hash value for two or more different inputs which is unavoidable and this is called hash collision. Hash functions are categorized into two parts

- Cryptographic hash functions
- Non Cryptographic hash functions

Probablistic data strctures uses non Cryptographic hash functions. The reason for the use of non-cryptographic hash function is that they're significantly faster than cryptographic hash functions.

## Membership Case Study

A membership problem for a dataset is a task to decide whether some element belongs to the dataset or not.