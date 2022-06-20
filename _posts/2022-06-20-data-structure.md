---
layout: post
title: Data Structure in Java
subtitle: Data structure is one of the fundamental pillars of computer engineering. Understanding the strengths and weaknesses of each of them offers a great tool to be able to solve problems in a much more efficient way.
categories: java
tags: [java, data structure]
---

Data structure refers to a data collection with well-defined operations and behavior or properties. A data structure is a unique way of storing or organizing the data in computer memory so that we can use it effectively.

## The need for Data Structures

As the amount of data grows rapidly, applications get more complex, and there may arise the following problems:

- Processing Speed: As the data is increasing day by day, high-speed processing is required to handle this massive amount of data, but the processor may fail to deal with that much amount of data.
- Searching data: Consider an inventory with a size of 200 items. If your application needs to search for a particular item, it needs to traverse 200 items in every search. This results in slowing down the search process.
- Multiple requests at the same time: Suppose, millions of users are simultaneously searching the data on a web server, then there is a chance of server failure.

In order to solve the above problems, we use data structures. Data structure stores and manages the data in such a way that the required data can be searched instantly.

## Data Structure Classification

- Linear Data Structures: In a linear data structure all the elements are arranged in the linear or sequential order. The linear data structure is a single level data structure.
- Non-Linear Data Structures: The non-linear data structure does not arrange the data in a sequential manner as in linear data structures. Non-linear data structures are the multilevel data structure.

![](https://evavillarrealguzman.github.io/img/data-structure/classification.png)

## Arrays 

An array also referred to as a collection of elements, is the simplest and most widely used Data Structure. Most of the Data Structures (e.g.Stack and Queue) were derived using the Array structure, which is why it is known as one of the central building blocks of Data Structures.

The purpose of an Array is to group similar kinds of data for fast access.

Look at the figure below; we have made a simple array with four elements. Each item in the collection is called a *Data Element*, and the number of data elements stored in an Array is known as its *size*. You can see that each data element has a maximum of two neighbors, except the first and last one.

![](https://evavillarrealguzman.github.io/img/data-structure/array.png)

The elements of an array are stored contiguously in memory. A physical space is saved in ram memory, one next to the other, to save the elements.

### Array Indexing

Each data element is assigned a numerical value called the *index*, which corresponds to the position of that item in the array. It is important to note that the value of the index is non-negative and always starts from zero. So the first element of an array will be stored at index 0 and the last one at index size-1.

**An index makes it possible to access the contents of the array directly**. Otherwise, we would have to traverse through the whole array to access a single element. That is the key feature that differentiates Arrays from Linked lists

### Types of Arrays

Arrays can store primitive data-type values (e.g., int, char, floats, boolean, byte, short, long, etc.), non-primitive data-type values (e.g., Java Objects, etc.) or it can even hold references of other arrays. That divides the arrays into two categories:

- One Dimensional Array
- Multi-Dimensional Array

In primitive array, values are stored in a contiguous memory location. Whereas, in the non-primitive array, objects are stored in the heap segment.

#### One-Dimensional Array

##### Declaration

In the array declaration, reference of an array is created. To declare an one-dimensional array, you have to specify the data type and name of the array.

```java
class OneDArray {
 public static void main(String args[]) {
  //Declaration Syntax
  int myArray1[];
  int[] myArray2;

 }
}
```

The above declarations will tell the compiler that reference variables ```myArray1``` and ```myArray2``` will hold an array of type ```int```. For now, no actual array exists. To link these reference variables with the actual physical array, we have to create one using the ```new``` operator.

##### Initialization

Array initialization actually gives memory to the elements of an array. The basic syntax for initializing an array is given below:

```java
arrayName = new type [size];
```

Example:

```java
class OneDArray {
 public static void main(String args[]) {
  //Declaration Syntax
  int myArray[];

  // Initialization Syntax
  myArray = new int [4];

  // Accessing elements in an array
  for (int i = 0; i < myArray.length; i++){
      System.out.println(myArray[i]);
  } 

 }
}
```

The program given above will create a space to store four ```int``` values contiguously. If we don’t specify the values of an array, it will be automatically initialized to 0.

##### Initialization and Declaration in One Step

We can also declare and initialize the array in one step.

```java
datatype[] arrayName = new datatype [size];
```

or 

```java
datatype arrayName[] = new datatype [size];
```

##### Adding or Updating Elements in an Array

To add or update the element in an array, we specify the particular index and assign the new value.

```java
arrayName[index] = value;
```

##### Adding Elements using Array Literal

If the size and the values of an array are known in advance, we can use the array literal for adding elements in an array.

```java
datatype[] arrayName = {Comma Separated list of values};
```

#### Two Dimensional Arrays

As discussed earlier, arrays can also store references as values. These references can point to anything. They can be a reference to an object or any other data structure. Before we move forward, let’s briefly discuss what **pointers** are:

- References are used to explicitly store memory locations that hold a value or an object.
- Any time you build an object in Java, you basically create a reference to that object.

A Two Dimensional Array is an array of references that holds references to other arrays. These arrays are preferably used if you want to put together data items in a table or matrix-like structure.

It is important to note that in 2D arrays, all values must have the same data type. This means that you can’t store an array of integers next to an array of strings and vice versa.

##### Initialization

Initialization in Two Dimensional Arrays is done using two values for the number of Rows and Columns. Look at the following code to see how 2D array is declared and initialized in Jav

```java
class TwoDArray {
    public static void main( String args[] ) {
      
      int rows = 3;
      int columns = 4;
      
      int [][] my2DArray;
      my2DArray = new int[rows][columns];
      
      System.out.println("You have successfully created a 2DArray");
       
    }
}
```

##### Adding or Updating Elements in an Array

To add or update the element in an array, we specify the index for the row and column, and then assign the new value.

```java
arrayName[indexRow][indexColumn] = value;
```

Example:

```java
class TwoDArray {
    public static void main( String args[] ) {
        int [][] my2DArray = new int[3][4];

      // add "10" at Row 0 and Column 1
        my2DArray[0][1] = 10;
        System.out.println(my2DArray[0][1]);
    }
}
```

### How are arrays stored in memory?

In Java, arrays are dynamically allocated. Arrays are stored in the memory using a reference pointer, which points to the first element. For example, if we create an array of size 3 and name it myArray, then the variable will point to the start of the array. See the figure below:

![](https://evavillarrealguzman.github.io/img/data-structure/array.png)

**The only drawback of using arrays is that we have to specify the size of the array during the time of instantiation**. That means the size remains fixed and can not be extended. If we want to add more elements, we will have to create a new array, copy all the items from the old array to the new one, and then insert the new element.

### Time complexities for array operations:

- Accessing elements: *O(1)*
- Searching:
    - For Sequential Search: *O(n)*
    - For Binary Search [If Array is sorted]:*O(log n)*
- Insertion: *O(n)*
- Deletion: *O(n)*

Access by index at any time is constant, since since all the elements are in contiguous memory space, knowing the initial memory address, you can easily calculate the memory position of the others.

However, this contiguity has its problems. In order to store all the elements contiguously, the computer has to find a memory space large enough to fit all this data. An insert of an element can have a linear cost, because it has to copy all the elements of the array to a new memory location.

## Linked Lists

A linked list is a linear data structure consisting of nodes where each node contains data and a pointer to the next node in the list.

**Linked lists overcome the drawbacks of arrays because in linked lists there is no need to define the number of elements before using it**, therefore the allocation or deallocation of memory can be during the processing according to the requirement, making the insertions and deletions much easier and simpler.

The elements in linked list are not contiguous in memory. They are distributed arbitrarily.

There are two kinds of linked lists:

- Singly linked list
- Doubly linked list

### Singly Linked List

A singly linked list can only be traversed in one direction, meaning, from the first node (head) towards the last node. The last node points to NULL, indicating the end of the list.

![](https://evavillarrealguzman.github.io/img/data-structure/single-linked-list.png)

#### Pros

- Insertion and deletion at head is more efficient compared to the corresponding operations in an array.
- Changes are localized when inserting and deleting somewhere in the middle of the linked list. In the case of an array, all subsequent elements need to be moved around. Of course, if you first have to search for the position to insert or delete in a linked list, then it still takes *O(n)* in the worst case.

#### Cons
- As each node also contains a pointer, it has a memory overhead.
- We cannot access a node through indexing directly, rather we have to traverse from the start to access an element at a given position n.
- Reverse traversing is not possible with a singly linked list.

### Doubly Linked List

Each node in a doubly linked list contains a pointer to the next node as well as pointer to the previous node. This allows the list to be traversed in both directions: forward and backward.

![](https://evavillarrealguzman.github.io/img/data-structure/double-linked-list.png)

Doubly linked lists have all the advantages of singly-linked lists while providing some additional pros and cons.

#### Pros
- Insert and delete at the head as well as tail are *O(1)* operations
- The list can be traversed forward as well as backward

#### Cons
- Have greater space overhead than singly-linked lists

### Basic Linked List Operations

| Operations       | Descriptions           |
|--------------|--------------|
| insertAtEnd(data)           | Inserts an element at the end of the linked list | 
| insertAtHead(data)           | Inserts an element at the start/head of the linked list | 
| delete(data)           | Deletes an element from the linked list | 
| deleteAtHead()	       | Deletes the first element of the list | 
| deleteAtEnd()	       | Deletes the last element of the list | 
| search(data)	       | Searches an element from the linked list | 
| isEmpty()	       | Returns True if the linked list is empty, otherwise returns False | 

If you observe the list of methods mentioned above, the last function, isEmpty() is a helper function which will prove useful in defining all the other functions.

### Time and space complexities

Deletion at the tail is one of the operations that distinguishes a doubly-linked list from a singly-linked list. The following table shows a side by side comparison of key operations on both.

| Operation       | Singly Linked List           | Doubly Linked List     |
|--------------|--------------|-----------|
| Insert at head           | *O(1)* | *O(1)*    |
| Delete at head           | *O(1)* | *O(1)*    |
| Insert at tail           | *O(n)* | *O(1)*    |
| Delete at tail	       | *O(n)* | *O(1)*    |

In a linked list, adding and removing elements is very easy. You just have to create the node and modify the pointer. To remove the element, you make the previous element's next pointer point to the next element. Therefore access is constant. The downside is that you can't quickly access the element as it is in arbitrary positions in memory, you always have to start from the beginning and iterate from pointer to pointer until you get to the element you need, so linked list has a linear access.

In terms of space, if there are *n* nodes in a linked list, *d* is the (average) number of bytes of data in each node and *p* is the number of bytes consumed by a pointer, then a singly linked list consumes *n(d+p)* bytes, whereas a doubly linked list consumes *n(d+2p)* bytes.



## Stacks

A stack is a collection of elements in which the storage and retrieval follows the Last in First Out (LIFO) ordering. This means that the last element added is the element on the top, and the first element added is at the bottom.

![](https://evavillarrealguzman.github.io/img/data-structure/stack.png)

A real-life example of Stack could be a stack of books. To get the book that’s somewhere in the middle, you will have to remove all the books placed at the top of it. Also, the last book you added to the Stack of books is at the top!

A stack can be physically implemented as an array or as a linked list. When we implement a stack as an array, it inherits all the properties of an array and if we implement it as a linked list, it acquires all the properties of a linked list.

### What are stacks used for?

Despite the simple implementation, stacks can be used to solve very complex problems!

There are many famous algorithms, such as Depth First Search and the Expression Evaluation Algorithm, which harness the functionality of stacks. Stacks are used:

- To backtrack to the previous task/state, for example in recursive code
- To store a partially completed task, for example, when you are exploring two different paths on a Graph from a point while figuring out the smallest path to the target.
- Nested Function calls
- Parenthesis matching

### Operations

- push(): adds an element to the stack
- pop(): removes the last inserted element from the stack
- top(): Returns the value of the last inserted element from the stack, without removing it
- size(): Returns the number of elements currently in the stack
- isEmpty(): Returns true if there are no elements in the stack
- isFull(): Returns true if the stack is full, false otherwise. This method is implemented only if there is a constraint on the maximum size of the stack

### Time complexities

| Operation       | Time Complexity           |
|--------------|--------------|
| isEmpty           | *O(1)* | 
| top           | *O(1)* | 
| size           | *O(1)* | 
| push	       | *O(1)* | 
| pop	       | *O(1)* |


## Queues

Similar to the stack, a queue is another linear data structure that stores the elements in a sequential manner. The storage and retrieval in a queue follows the FIFO principle, which is short for First in First Out.

![](https://evavillarrealguzman.github.io/img/data-structure/queu.png)

In other words, in a queue, the first element inserted is the one that comes out first. You can think of a queue as a pipe with both ends open. Elements enter from one end (back) and leave from the other (front).

Queues are implemented in many ways. They can be represented by using an array, a linked list, or even a stack.

### What are queues used for?

Just like stacks, queues are used widely in searching and sorting algorithms such as the Breadth-First Search algorithm.

Most operating systems also perform operations based on a Priority Queue that allows operating systems to switch between appropriate processes. They are also used to store packets on routers in a certain order when a network is congested. Implementing a cache also heavily relies on queues.

We generally use Queues when:

- We want to prioritize something over another
- A resource is shared between multiple devices
- For breadth-first searching in special data structures like graphs and trees
- Queues are useful in telephone inquiries, reservation requests, traffic flow, etc. While using telephone directory service, you might have sometimes heard “Please wait, You are in A QUEUE”.

### Operations

The key operations on a queue are as follows:

enqueue(): Adds an element to the tail of the queue
dequeue(): Removes the element from the front of the queue
front(): Returns the element from the front of the queue without actually removing it from the queue
size(): Returns the number of elements currently in the queue
isEmpty(): Returns true if the queue is empty, false otherwise
isFull(): Returns true if the queue is full, false otherwise. This is only implemented if there is a constraint on the maximum size of the queue

## Trees

Trees consist of *vertices* (nodes) and *edges* that connect them. An edge is nothing but an ordered pair of nodes. Unlike the linear data structures, trees are *hierarchical*. We focus on *rooted* trees, here, in which, one of the nodes has a special significance and is designated the *root* of the tree.

An edge connects two nodes in a tree. If *(u, v)* is an edge in a tree, then *u* is the parent of *v* and *v* is a child of *u*. Each node in a tree contains a *value*. Trees are similar to Graphs, except that a cycle cannot exist in a Tree - they are acyclic. In other words, there is always exactly one path between any two nodes.

The following terminology is important:

- Root Node: A node with no parent nodes. Generally, trees don’t have to have a root. However, rooted trees have one distinguished node and are largely what we will use in this course.
- Parent and Child Nodes: If *u* and *v* are nodes in a tree and *(u, v)* is an edge in the tree, then *u* is the parent of *v*. Conversely, *v* is a child of *u*. The root node has no parent
- Sibling Node: Nodes that have the same Parent Node
- Leaf Node: A node that doesn’t have any Child Node
- Ancestor Nodes: All the nodes on the path from a node *u* to the root node are ancestors of *u*. In other words, ancestors of a node include its parents, grandparents, and so on.
- Descendant Nodes: The children, grand children and so on, of a node
- Sub-tree: The tree under a given node is its sub tree. It includes all of its descendants
- Degree of a node: Total number of children of a node
- Path: The path between two nodes *u* and *v* is an alternating sequence of nodes and edges, which starts at *u* and ends at *v* with edges that are present in the tree and no node is  repeated. In a tree, there is exactly one path between any pair of nodes
- Length of a path: The number of edges in a path
- Depth of a node n: The length of the path from a node n to the root node. The depth of the root node is 0.
- Level of a node *u*: (Depth of a Node *u*)+1
- Height of a node *u*: The length of the path from *u* to its deepest descendant. The height of the leaf nodes is always 0.
- Height of a Tree: Height of the root node

The figure below shows most of the terminologies described above:

![](https://evavillarrealguzman.github.io/img/data-structure/tree.png)

- 1 is the root of this tree
- 1 is the parent of 2, 3 and 4. Also, 3 is the parent of 6 and 7
- 2, 3 and 4 are children of 1. Also, 6 and 7 are children of 3
- 2, 4, 6 and 7 are leaf nodes
- 2, 3 and 4 are siblings of one another. Also, 6 and 7 are siblings
- The root node is at level 1. Its children are at level 2 and so on
- The root node is at depth 0. Its children are at depth 1 and so on
- The leaf nodes are at height 0. Every other node has height 1 greater than the height of its "tallest" child

### Binary tree

In a binary tree, any node can have at most two children. A binary tree is called a full (or proper) binary tree if every node other than the leaf nodes has exactly two children. A binary tree is called a complete binary tree if every level, except possibly the last one, are completely filled with nodes and the leaf nodes are as far left as possible.

### Implementation

There are different ways to implement trees. A common approach is:

- Declare a class to represent a node in the tree
- If it is a binary tree, have pointers to the left and right children. Otherwise, have a pointer to a list of children
- Maintain a root pointer that holds a reference to the root of the tree

## Tries

Tries are prefix trees that are used to store strings for fast retrieval. A node in a Trie represents a letter in an alphabet. For insertion, the tree takes a string as input and then creates a new node for each character of that string/word. The node registered for the last character of the string/word is marked as the end of word. A Boolean variable is usually used to do this.

The illustration below explains how a prefix tree is created:

![](https://evavillarrealguzman.github.io/img/data-structure/trie.png)

1- Insert 'their'  
2- Creating node with 't'  
3- Creating node with 'h'  
4- Creating node with 'e'  
5- Creating node with 'i'  
6- Creating node with 'r'  
7- Marking 'r' as the end of the word  
8- Insert 'there'  
9- Path till 'the' is same  
10- Now, creating new nodes for 'r' and 'e'  
11- Marking 'e' as the end of the word  

### Trie node

A typical Node in a Trie consists of two data members:

- children: An array that consists of the children nodes. The size of this array depends on the number of alphabets (26 for the English language). By default, all elements are set to Null.
- isEndWord: A flag to indicate the end of a word. It is set to false by default and is only updated when an end of the word is observed during insertion.

#### Methods

The following are the common operations performed by tries:

- Insert(String): Inserts a string into the trie.
- Delete(String): Deletes an existing string from the trie.
- Search(String): Searches if the input string exists as a complete word in the trie.
- prefixSearch(String): Searches if the prefix string exists in the trie.

## Heaps

Heaps are a data structure primarily used for efficiently finding the smallest or largest element in an array. Heaps are advanced data structures based on **Binary Trees**, which is why they are commonly known as Binary Heaps.

Heaps are used as priority queues that are used when we want to coordinate certain elements based on a certain value. Thanks to this hierarchy of nodes, the heaps are very fast (in fact they are constant) when it comes to obtaining both the minimum and the maximum. The downside is that inserting or removing elements can have a logarithmic cost. Certain node move operations must be done each time I insert or delete in order to ensure that the property is maintained.

### Implementation

Heaps are implemented through Arrays, where each element of the array corresponds to a node in the binary tree and the value inside the node is called a “key”. Heaps can also be implemented using trees with a node class and pointers, but it’s usually easier and more space efficient to use an array.

All the nodes are ordered according to the rules listed below:

- A Heap tree must be a Complete Binary Tree.
- The nodes must be ordered according to the Heap Property.

### Heap property

A heap is built, based on the Heap property, by comparing the parent node key with its child node keys. This comparison is done based on the heap property. The two heap structures are:

- Min Heap
- Max Heap

Min Heap is built on the Min Heap property, and Max Heap is implemented on the Max Heap property. Let’s see how they are different.

#### Max Heap property

This property states that all the parent node keys must be greater than or equal to their child node keys. So the root node, in this case, will always contain the largest element present in the Heap. If Node A has a child node B, then, key(A) >= key(B)

![](https://evavillarrealguzman.github.io/img/data-structure/max-heap.png)

#### Min Heap property

In Min Heap, all the parent node keys are less than or equal to their child node keys. This goes without saying that the rule will apply to all children of the node. So the root node, in this case, will always contain the smallest element present in the Heap. If Node A has a child node B, then, key(A) <= key(B)

![](https://evavillarrealguzman.github.io/img/data-structure/min-heap.png)

### Operations

Following operations can be performed on a heap:

- Insertion: It has O(log(n)) O(log(n)) time complexity.
- Remove min/max: It has O(log(n)) O(log(n)) time complexity.

### Heap Representation in Arrays

Heaps can be implemented using Arrays. The contents of a heap with n nodes are stored in such a way that all the parent nodes occur in the first half of array (n/2), while the leaves are present at the last n/2 positions. So the last parent will be at the n/2th position.

The node at the kth position will have its children placed as follows:

- The Left child at 2k+1
- The Right child at 2k+2.

See the figure below to see how nodes are mapped on the array:

![](https://evavillarrealguzman.github.io/img/data-structure/heap-array.png)

In the figure above, you can see that all of the parent nodes are present in the first half of the array, with the last parent at the n/2th position (i.e. 4th index), whereas the children nodes appear on the second half. The following properties also hold:

    Left Child = 2k+1
    Right Child = 2k+2

## Hash Tables

A hash table stores elements in pairs, key and value. The key is what will determine where the value will go, to which memory location. In order to convert the key to an index, a hash function is used. The hash function receives the key as input and returns a memory location or index to determine where the value is going to be. With this we gain that access to the elements is constant. The key is directly converted to a memory function. The inserts are also constant.

A hash table is a data structure that is used to search and retrieve elements in constant time. Hash tables apply a technique called hashing to uniquely identify each element. Elements or values are stored at some pre-calculated unique index called key. So every entry stored in the form of a key-value pair, and the collection of such items is called “Dictionary”. Each object can be searched using that key in *O(1)*.

### Internal working

Hash Tables are generally implemented using Arrays, as they are the only data structures that provide access to elements in constant *O(1)* time.

#### Key-value pair

So the idea of data retrieval in *O(1)* is executed by using a key to map the data on an array (there are many ways to compute this key). In the case of arrays, you can directly use the key as an index to store data. If you pass the key to the array, the value is retrieved; alternatively (in the most naive form),

    value = arr[key]

Here’s an illustration of how a Hash Table is mapped to the indices (1,2,3,…,5) in an array, with the index of this array calculated through a pre-defined function.

![](https://evavillarrealguzman.github.io/img/data-structure/hash.png)

### Trie vs Hash Table

One might wonder what is the need of using Tries when we can implement dictionaries with Hash Tables as well. A simple answer to this would be, yes, you can use Hash Tables to build dictionaries, but if you need a fast lookup and have long words which share common prefixes then a Trie is the perfect data structure for you. It also makes storing words easier, as the implementation is very simple. Some of the key points which differentiate a Hash Table from Tries are given below:

#### 1. Common Prefixes

In Tries, the words having common prefixes share a common path until the end of the prefix. After that, they are divided into two branches. We cannot do that in Hash Tables; no matter how similar the words are, we would still need to store them at different locations. This would result in irrelevant iterations while searching. Here is an interesting example to explain what we just said: two words “interest” and “interesting” will be stored differently in a HashTable, but in a Trie we only need to add 3 new nodes for “ing” at the end of the “t” Node. Did you notice the space efficiency?

####  2. Lookup for Exact Words
As discussed in the previous lesson, Tries can perform a spell-check, but in Hashing. We can only look up exact words, otherwise, it will not be able to identify the word.

#### 3. No Re-hashing Required

The most important part of a HashTable is the Hash function. It is often very difficult to build as the performance of HashTable is entirely dependent on it. But in Tries, we do not need to perform re-hashing to generate an index. It just traverses the nodes and inserts new nodes, that’s it!

#### 4. Collision Resolution

One downside of using a HashTable is that we always need to come up with good collision resolution strategies to avoid collisions if the data is huge. A collision can never occur in Trie because all words are unique and can act like a “key”. This makes the implementation of Tries so much simpler!

#### 5. Memory Requirements

In worst case scenarios, a Trie will definitely perform better than a HashTable, but HashTables will be more convenient to use in average cases-- depending upon the efficiency of the Hash function. As in Trie, we need to allocate 26 pointers at every node even if most of them are Null, so a HashTable would be more of a wise choice here!

If your dictionary contains a lot of words with common prefixes or suffixes then Tries would be an efficient data structure to use as compared to Hash-Tables.

### HashMap vs HashSet

Before we solve any challenges regarding Hast Tables, it is necessary to look at the definitions of HashMap and HashSet and see how they are different. Both are implemented in Java using the Hash Table class, which is why it is also a common misconception that these two structures are the same, but they are very different from each other

HashMap is a collection that contains all the key-value pairs; it maps the values to keys. There is a built-in class available in Java for HashMap, implemented by using Map interface. It provides the basic functionality of hashing along with some helper functions that help in the process of insertion, deletion, and search.

Some of the key features of HashMap are given below:

- A HashMap stores key-value pairs (examples given below) to map a key to the value:

    abc->123

    xyz->456

- HashMap cannot contain duplicate keys. It can, however, have duplicate values.
- HashMap also allows multiple null values and only one null key
- This mechanism does not support synchronous operations and is not thread-safe.

HashSet class is implemented in Java using Set interface. It is also built in the same way as HashMap, i.e., using the Hash Table class, but it is still quite different from the HashMap class. Some of the key features of HashSet are listed below:

- HashSet also stores values in an unordered way, using hashing, but this happens in the backend. On the backend, the HashSet class is implemented using the HashMap class. The value that we add to the HashSet is then added to the HashMap as a key, corresponding to a dummy value Object. The retrieval remains *O(1)*
- HashSet is a class which implements the Set interface and this interface only stores values, not a key-value pair.
- HashSet restricts storing multiple null values and only allows one null value in the whole table
- HashSet does not allow storing duplicate values as a set can only contain unique elements
- Just like HashMap, HashSet also does not support synchronous operations

## Graphs

A graph is a set of nodes that are connected to each other in the form of a network. Graphs are exceptional for representing entity relationships. For example the distances between different cities or the interactions between different people.

Let’s define the two basic components of a graph.

- **Vertex**: A vertex (or node) is an essential part of a graph. A collection of vertices forms a graph. In that sense, vertices are similar to linked list nodes.
- **Edge**: An edge is a link between two vertices. It can be directed or undirected. An undirected edge *(u, v)* has no sense of direction and can be traversed in either direction - *u* to *v* or *v* to *u*. A directed edge *(u, v)*, on the other hand can only be traversed from *u* to *v*. Visually, a directed edge is drawn with an arrow and an undirected edge is drawn without an arrow.

> An edge can exist from one vertex to itself. This is called a self loop

Here is a visual representation of a graph:

![](https://evavillarrealguzman.github.io/img/data-structure/graph.png)

- **Path**: A path between nodes *u* and *v* in a graph is an alternating sequence of nodes and edges, such that it:
  - starts with *u*
  - ends with *v*
  - all nodes and edges in the path are present in the graph
- **Cycle**: A path that starts and ends at the same node in a graph, is called a cycle.

### Types of graphs

Graphs can be categorized across multiple criteria, such as:

#### Undirected vs directed

In an undirected graph, all the edges are undirected, whereas in aa directed graph, all the edges are directed.

![](https://evavillarrealguzman.github.io/img/data-structure/graph2.png)

#### Weighted vs unweighted graph

In an unweighted graph, the edges have no weight associated with them. The above examples are both unweighted graphs. When representing a social network, for example, where an edge represents friendship between two people, no weight is needed on the edge.

In a weighted graph, each edge is associated with a weight. The weight may be used to represent certain properties of a problem. For instance, if an edge represents a road between two cities, the distance between the two cities may be represented as the edge weight.

#### Connected graph

An undirected graph in which there is at least one path between any two pairs of nodes is called a connected graph.

> A tree is a connected graph in which there are no cycles

### Shortest path

When we talk about path in a graph, we are mostly interested in the shortest path between two vertices in a graph. A shortest path in a weighted graph is the one for which the sum of edge weights is minimum. In case of an unweighted graph, the shortest path is the one with the minimum number of edges.

### Ways to represent a graph

The two most common ways to represent a graph are:

#### Adjacency matrix

The adjacency matrix is a two-dimensional matrix where each cell can contain a 0 or a 1.​ The row and column headings represent the vertices.

If a cell contains 1, there exists an edge between the corresponding vertices e.g., *Matrix[0][1]=1* shows that an edge exists between vertex 0 and 1.

![](https://evavillarrealguzman.github.io/img/data-structure/adjacency-matrix.png)

#### Adjacency list

An array of linked list is used to store all the edges in the graph. The size of the array is equal to the number of vertices.

The entry at index i of the array contains a linked list containing the vertices that are adjacent to vertex i.

![](https://evavillarrealguzman.github.io/img/data-structure/adjacency-list.png)

> The above examples represented unweighted graphs using adjacency matrix and list. To represent weighted graph, instead of 1, we need to place the weight of the corresponding edge.

### What is a Bipartite Graph?

A Bipartite Graph is a special kind of Graph, in which the vertices can be divided into two disjoint sets U and V such that no vertex of U is adjacent to any other vertex in U and no vertex of V is adjacent to any other vertex in V. Vertices in U have edges that connect it to vertices in V.

![](https://evavillarrealguzman.github.io/img/data-structure/bipartite.png)

> Note: All the cycle graphs, i.e graphs that consist of a single cycle, or some number of vertices (at least 3) connected in a closed chain, are bipartite.

#### Explanation

The above graphs are Bipartite.

1- Yellow-colored vertices belong to a disjoint set U.  
2- Green colored vertices belong to a disjoint set V.  

One can observe in the above graphs that no vertex of U is adjacent to any other vertex in U, and no vertex of V is adjacent to any other vertex in V.

#### Can a Cycle Graph be Bi-Partite with Even Vertices?

The illustration below shows a cycle graph with an even number of vertices.

![](https://evavillarrealguzman.github.io/img/data-structure/bipartite2.png)

But is the above graph Bipartite? Yes!

How? You can see that the graph on the left has an even number of nodes, which means that they can be divided into two disjoint sets, shown on the right side, with non-adjacent vertices.

Note: A graph cannot be Bipartite if there are an odd number of vertices and has an odd cycle i.e., a cycle between the odd number of vertices.

### Types of bipartite graphs

Some other types of Bipartite graphs are listed below:

- Complete Bipartite Graphs: A complete Bipartite graph connects each vertex from set V1 to each vertex from set V2.
- Star Graphs: A star graph is a complete bipartite graph if a single vertex belongs to one set, and all the remaining vertices belong to the other set.
- Acyclic Graphs: A graph with no cycles is called an acyclic graph.

![](https://evavillarrealguzman.github.io/img/data-structure/bipartite3.png)

# Sources

- [Ace the Java Coding Interview](https://www.educative.io/path/ace-java-coding-interview)
- [Data Structure in Java – A Complete Guide for Linear & Non-Linear Data Structures](https://techvidvan.com/tutorials/data-structure-in-java/)
- [Data Structures & Algorithms using Java – A Beginners Guide](https://www.mygreatlearning.com/blog/data-structures-using-java/)
- [6 ESTRUCTURAS de DATOS que todo INGENIERO debería CONOCER](https://www.youtube.com/watch?v=5k2DWMRTXMM)