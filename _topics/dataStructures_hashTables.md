---
topic: "Data Structures: Hash Tables"
desc: "Open and Closed Hashing, etc."
---

All hash tables have in common the idea that a hash function $$H(x)$$, maps a value to a location.   For simplicity, we'll treat this location as the "index of an array of size n", i.e. an integer value between 0 and n-1.

This scheme works perfectly when there are no hash collisions.
* A hash collision happens when $$x_1\ne x_2$$, but  $H(x_1)=H(x_2)$.
* Example: For H(x)=x%100, H(201) == H(401)

The best way to deal with this is to choose a clever hash function&mdash;one that takes the statistical distribution of your data into account, and choose a scheme that makes collisions very unlikely.     

But, since the number of data values you have is usually much larger than the size of your array, by the Pigeonhole Principle (which you learned about, or will learn about in CS40), there will ALWAYS be some collisions.  What to do?   

There are two basic approaches, with some variations on each. Sadly, the terminology used for these techniques can be quite confusing.

One way of thinking about the two approaches:
* Does the array store objects themselves&mdash;in this case, when there's a collision you go "probing" for the value, i.e. iterating through the table until you are satisfied you can't find the item.  The most basic approach, <em>linear probing</em>, just iterates by 1 (with wrap around) until an empty slot is found.   Variations on that use more elaborate ways of skipping from one item to the next.
* Does the array store a pointer to a data structure containing the items that hash to a particular key&mdash;e.g. a linked list of those items.

The terminology can gets confusing here.   

{| class="wikitable"
|-
| How it works
| Hashing&mdash;do we leave the hash table, or not, to find data?
| Addressing&mdash;how do we determine an address?
| Other names that may apply
|-
| All elements are in the array, and you go probing through the array to resolve collisions.
| Closed Hashing: nothing is outside the hash table
| Open Addressing: the address is not determined only by the key, but also by what else is in the hash table
| There are various kinds of probing: linear probing, quadratic probing, double hashing
|-
| The element are outside the array; each array element is the head of a linked list (or other data structure)
| Open Hashing: we may have to go outside the hash table to find our data
| Closed Addressing: the hash function tells us exactly where to go (though we may have to iterate through a list once we get there.)
| <b>Separate Chaining</b> is the term often used for this technique.
|}



= Intro =

== The Underlying Idea ==

Below is the a summary of the three most common operations supported by a Hash Table. Each operation is listed with its average case and its worst case complexity.

<div style="font-family:courier; text-indent: 50px; font-size:130%" id="ADT">
<table>
  <tr>
     <td>
       <p> find(type item) </p> 
    </td>
    <td>
       <p> O(1) in average case </p>
     </td>
    <td>
       <p> O(n) in worst case </p>
    </td>
  </tr>
  <tr>
     <td>
       <p> insert(type item) </p>
    </td>
    <td>
       <p> O(1) in average case </p>
     </td>
     <td>
       <p> O(n) in worst case </p>
     </td>
  </tr>
  <tr> 
     <td>
       <p> delete(type item) </p>
    </td>
    <td>
       <p> O(1) in average case </p>
     </td>
     <td>
       <p> O(n) in worst case </p>
     </td>
  </tr>
</table>
</div> 

<p style="font-size: 110%"> Hash Tables are a data structure that take advantage of <b>random access</b> to achieve extremely fast lookups. "Random Access" in this case is a term of art that means we can directly access a piece of data at any arbitary address in memory (i.e. RAM).     The term RAM for memory refers to this property&mdash;it is an acronym for "random access memory".

An array in C++, for example, supports random access: you can index into an array by adding an offset (computed by the array index) to the base address, and directly retrieve any element in one operation.

----

<div style="font-size:130%" id="hash_tables_ex">

<p style="font-size:150%"><b> Open Addressing and Separate Chaining</b></p>

Think of a Hash Table as a contiguous array-like structure. As each input value is read, it is analyzed and given a place to be stored in the array. This routine is commonly referred to as <b>indexing</b>, or <b>hashing</b>. The random nature of this placement, however, inevitably leads to data being indexed, or assigned, to an already occupied space in the array. Such an event is called a <b>collision</b>. Both Open Addressing and Separate Chaining were created as strategies to handle, or resolve, collisions. 


We construct both an Open Addressing and Separate Chaining Hash Table for the same set of input. This will help clarify the similarities and differences between both structures. To give each input value a storage space, a routine called a <b>hash function</b> assigns an index to any given input value. This function could in theory be as simple as a direct-mapped strategy (e.g. H(x) = x). This is not a very strong hash function, however. Classically the hash function is of the form <b>H(x) = value % table-size</b>. 


A Brief Consideration: % is an extremely powerful operation in this context. In this case, it has the power to reduce an arbitrarily large number to a value strictly less than the total number of unique slots in the hash table's array (by repeatedly subtracting factors of table-size). If the input to a hash table is {2, 1000000000000} where H(x) does not use %, the hash table would have be enormously space inefficient. Modulo could reduce 1000000000000 to a single digit making the table-size less than 10, a far more reasonable size for the number of input values.  

<p style="font-size:80%">[Note: ignore the superscript [number] in each cell; they will be used very soon to help navigate each Hash Table's construction]</p>

<div id="tables">
<table border="2" width="50%" align="center" style="font-size=120%; text-align:center">
  <tr height="5">
    <td><b> Input</b></td>
    <td>[82, 32, 67, 149, 11]</td>
  </tr>
  <tr  height="5">
    <td><b>Hash Function</b></td> 
    <td>H(x) = (x) % table-size</td> 
  </tr>
</table>



<div align="center">
<table border="2" style="text-align:center; display: inline-block; float: left; margin-left: 20%">
  <tr>
     <td><p style="font-family:courier">Index </p></td>
     <td><p style="font-family:courier">Value </p></td>
  </tr>
  <tr>
     <td>0</td>
     <td> </td>
  </tr>
  <tr>
     <td>1</td>
     <td>11<sup>[5]</sup></td>
  </tr>
  <tr>
     <td>2</td>
     <td>82<sup>[1]</sup></td>
     <td>32<sup>[2]</sup></td>
     <td>67<sup>[3]</sup></td>
  </tr>  
  <tr>
     <td>3</td>
     <td></td>
  </tr>  
  <tr>
     <td>4</td>
     <td>149<sup>[4]</sup></td>
  </tr>  
</table>

<table border="2" style="text-align:center; display: inline-block">
  <tr>
     <td><p style="font-family:courier">Index</p></td>
     <td><p style="font-family:courier">Value</p></td>
  </tr>
  <tr>
     <td>0</td>
     <td>149<sup>[4]</sup></td>
  </tr>
  <tr>
     <td>1</td>
     <td>11<sup>[5]</sup></td>
  </tr>
  <tr>
     <td>2</td>
     <td>82<sup>[1]</sup></td>
  </tr>  
  <tr>
     <td>3</td>
     <td>32<sup>[2]</sup></td>
  </tr>  
  <tr>
     <td>4</td>
     <td>67<sup>[3]</sup></td>
  </tr>  
</table>
</div>

<div style="font-size:80%" id="chained_hash" > 
<p><b>Chained Hash Table:</b><i>left table</i></p>

<p>[1] Value 82 is read into H(x). H(x) = <u>82 % 5 = 2</u> &emsp; => Element 82 hashes to index 2. No collisions.</p>
<p>[2] Value 32 is read into H(x). H(x) = <u>32 % 5 = 2</u> &emsp; => Element 32 hashes to index 2. Index 2 is already occupied causing a collision. 32 becomes the tail of a linked list whose head is 82.</p>
<p>[3] Value 67 is read into H(x). H(x) = <u>67 % 5 = 2</u> &emsp; => Element 67 hashes to index 2. Again, the collision is handled by placing 67 at the end of a linked list whose head is 82.</p>
<p>[4] Value 149 is read into H(x). H(x) = <u>149 % 5 = 4</u> &emsp; => Element 149 hashes to index 4. No collisions.</p>
<p>[5] Value 11 is read into H(x). H(x) = <u>11 % 5 = 1</u> &emsp; => Element 11 hashes to index 1. No collisions.</p>
</div>



<div id="linear_hash" style="font-size:80%"> 
<p><b>Linear Probing Table:</b><i>right table</i></p>

<p>[1] Value 82 is read into H(x). H(x) = <u>82 % 5 = 2</u> &emsp; => Element 82 hashes to index 2. No collisions.</p>
<p>[2] Value 32 is read into H(x). H(x) = <u>32 % 5 = 2</u> &emsp; => Element 32 hashes to index 2. Index 2 is already occupied causing a collision. Linear probing looks at index + 1 for an empty slot. Because location 3 in the Hash Table is empty, element 32 is placed into index 3.</p>
<p>[3] Value 67 is read into H(x). H(x) = <u>67 % 5 = 2</u> &emsp; => Element 67 hashes to index 2. Again, index 2 is already occupied. Linear probing looks for an empty location in (index + 1). But, (index + 1) is already occupied. It then looks in (index + 2) which is empty. Element 67 hashes to index 4. </p>
<p>[4] Value 149 is read into H(x). H(x) = <u>149 % 5 = 4</u> &emsp; => Element 149 hashes to index 4. In this case, element 67 was stored at index 4. A collision has occurred. Linear probing searches for an empty slot at position (index + 1). But index 5 does not exist! Instead, we wrap around the table to continue linearly searching starting from index 0. Element 0 is empty, and 149 is inserted into index 0.</p>
<p>[5] Value 11 is read into H(x). H(x) = <u>11 % 5 = 1</u> &emsp; => Element 11 hashes to index 1. No collisions.</p>
</div>

</div> 
----

<div style="font-size:130%" id="find_analysis">

<p style="font-size:150%"><b> Find</b>: O(n) worst case time</p>

All examples including this one use the two hash tables previously constructed to help makes things clearer. 


<div align="center">
<table border="2" style="text-align:center; display: inline-block; float: left; margin-left: 20%">
  <tr>
     <td><p style="font-family:courier">Index </p></td>
     <td><p style="font-family:courier">Value </p></td>
  </tr>
  <tr>
     <td>0</td>
     <td> </td>
  </tr>
  <tr>
     <td>1</td>
     <td>11</td>
  </tr>
  <tr>
     <td>2</td>
     <td>82</td>
     <td>32</td>
     <td>67</td>
  </tr>  
  <tr>
     <td>3</td>
     <td></td>
  </tr>  
  <tr>
     <td>4</td>
     <td>149</td>
  </tr>  
</table>

<table border="2" style="text-align:center; display: inline-block">
  <tr>
     <td><p style="font-family:courier">Index</p></td>
     <td><p style="font-family:courier">Value</p></td>
  </tr>
  <tr>
     <td>0</td>
     <td>149</td>
  </tr>
  <tr>
     <td>1</td>
     <td>11</td>
  </tr>
  <tr>
     <td>2</td>
     <td>82</td>
  </tr>  
  <tr>
     <td>3</td>
     <td>32</td>
  </tr>  
  <tr>
     <td>4</td>
     <td>67</td>
  </tr>  
</table>
</div>

</div>

<p> Consider a <b>Find(32)</b> operation on both tables: </p>

<div style="font-size:80%" id="find_operation" > 
<p><b>Chained Hash Table:</b><i>left table</i></p>

<p>32 hashes to 2 (when hashed by the same H(x) used to construct the table). The search operation looks for 32 in position 2. If it is not found, the search operation continues to search the linked list until reaching end, or finding the element in question.</p>
<p>In this case, 32 is a node in the linked list based on index 2.</p>

<p><b>Linear Probing Table:</b><i>right table</i></p>

<p>32 hashes to 2 (when hashed by the same H(x) used to construct the table). The search operation looks for 32 in position 2. If it is not found, the search operation begins linearly probing the table (e.g. look 1 step away from the current index, then 2 steps away from the current index and so on). If when searching, the element in question is found, the element is present and the routine exited. If not found, the search routine will continue until looking at "table-size" steps away from the index (e.g. you've searched the entire table and are back at the start). When searching, wrapping from the highest index of the table to index 0 in the table is required. In this case, 32 is found at position 3 (index + 1).</p>
</div>



<p> Time Analysis of an arbitrary <b>Find(x)</b> operation on both tables: </p>
<div style="font-size:80%" id="find_time_analysis" > 
<p><b>Chained Hash Table:</b><i>left table</i></p>
<p>Consider an input size of n elements. If all n elements map to the same location (e.g. a bad hash function like H(x) = <i>a</i> where <i>a</i> is a fixed constant), a linked list that is n elements long would be built. A search operation in the worst case, would have to check all elements in this linked list to see if an element was present or not. While this completely defeats the purpose of using a Hash Table, a cleverly chosen Hash function usually prevents this from happening. More reasonably, a search operation only takes a handful of calculations to show whether an element is in the Hash Table or not. A fair average case is O(1). </p>

<p><b>Linear Probing Table:</b><i>right table</i></p>
<p>Consider an input size of n elements. If all n elements map to the same location (e.g. a bad hash function like H(x) = <i>a</i> where <i>a</i> is a fixed constant), all elements would map to the same and linear probing would result in primary clustering. All elements are filled in one after another linearly away from index <i>a</i>. While this completely defeats the purpose of using a Hash Table, a cleverly chosen Hash function usually prevents this from happening. More reasonably, a search operation only takes a handful of calculations to show whether an element is in the Hash Table or not. A fair average case is O(1). </p>
</div>
</div>

----

<div style="font-size:130%" id="insert_analysis">

<p style="font-size:150%"><b> Insert</b>: O(n) worst case time</p>

Again, consult the two hash tables we previously constructed. Since the initial construction of these Hash Tables was a successive number of inserts that was heavily detailed, only time analysis below is given.

<div align="center">
<table border="2" style="text-align:center; display: inline-block; float: left; margin-left: 20%">
  <tr>
     <td><p style="font-family:courier">Index </p></td>
     <td><p style="font-family:courier">Value </p></td>
  </tr>
  <tr>
     <td>0</td>
     <td> </td>
  </tr>
  <tr>
     <td>1</td>
     <td>11</td>
  </tr>
  <tr>
     <td>2</td>
     <td>82</td>
     <td>32</td>
     <td>67</td>
  </tr>  
  <tr>
     <td>3</td>
     <td></td>
  </tr>  
  <tr>
     <td>4</td>
     <td>149</td>
  </tr>  
</table>

<table border="2" style="text-align:center; display: inline-block">
  <tr>
     <td><p style="font-family:courier">Index</p></td>
     <td><p style="font-family:courier">Value</p></td>
  </tr>
  <tr>
     <td>0</td>
     <td>149</td>
  </tr>
  <tr>
     <td>1</td>
     <td>11</td>
  </tr>
  <tr>
     <td>2</td>
     <td>82</td>
  </tr>  
  <tr>
     <td>3</td>
     <td>32</td>
  </tr>  
  <tr>
     <td>4</td>
     <td>67</td>
  </tr>  
</table>
</div>


<p> Time Analysis of an arbitrary <b>Insert(x)</b> operation on both tables: </p>
<div style="font-size:80%" id="insert_time_analysis" > 
<p><b>Chained Hash Table:</b><i>left table</i></p>
<p>Consider an input size of n elements. If all n elements map to the same location (e.g. a bad hash function like H(x) = <i>a</i> where <i>a</i> is a fixed constant), a linked list that is n elements long is built. The next inserted element must traverse the entire linked list of n elements to become the tail of the linked list at location <i>a</i>. Usually though, the hash function H(x) is designed to make insertion more random. Fewer collisions would arise to begin with. More reasonably, an insert, like search, operation only takes a handful of calculations to find an empty spot. A fair average case is O(1). </p>


<p><b>Linear Probing Table:</b><i>right table</i></p>
<p>Consider an input size of n elements. If all n elements map to the same location (e.g. a bad hash function like H(x) = <i>a</i> where <i>a</i> is a fixed constant), elements are inserted linearly apart from index <i>a</i>. Primary clustering with n elements occurs at index <i>a</i>. The next inserted element must probing n times in order to find the next empty slot (e.g. one after the nth element).  More reasonably, an insert operation only takes a handful of calculations to find an empty spot for the next element since clustering would be minimized to begin with. A fair average case is O(1). </p>

<p><b>Note</b>: insert for both tables can be dramatically reduced by simply double the capacity of the Hash Table. This allows for a greater number of unique indexes and thus less chance of collision or clustering. The traditional strategy is to double table-size and find the nearest prime number. That makes a Hash Function extremely unlikely to collide for a unique set of input. </p>
</div>

</div>

----

<div style="font-size:130%" id="delete_analysis">

<p style="font-size:150%"><b> Insert</b>: O(n) worst case time</p>

Once more, consult the two hash tables previously constructed. 

<div align="center">
<table border="2" style="text-align:center; display: inline-block; float: left; margin-left: 20%">
  <tr>
     <td><p style="font-family:courier">Index </p></td>
     <td><p style="font-family:courier">Value </p></td>
  </tr>
  <tr>
     <td>0</td>
     <td> </td>
  </tr>
  <tr>
     <td>1</td>
     <td>11</td>
  </tr>
  <tr>
     <td>2</td>
     <td>82</td>
     <td>32</td>
     <td>67</td>
  </tr>  
  <tr>
     <td>3</td>
     <td></td>
  </tr>  
  <tr>
     <td>4</td>
     <td>149</td>
  </tr>  
</table>

<table border="2" style="text-align:center; display: inline-block">
  <tr>
     <td><p style="font-family:courier">Index</p></td>
     <td><p style="font-family:courier">Value</p></td>
  </tr>
  <tr>
     <td>0</td>
     <td>149</td>
  </tr>
  <tr>
     <td>1</td>
     <td>11</td>
  </tr>
  <tr>
     <td>2</td>
     <td>82</td>
  </tr>  
  <tr>
     <td>3</td>
     <td>32</td>
  </tr>  
  <tr>
     <td>4</td>
     <td>67</td>
  </tr>  
</table>
</div>

<p> Consider a <b>Delete(32)</b> operation on both tables: </p>

<div style="font-size:80%" id="find_operation" > 
<p><b>Chained Hash Table:</b><i>left table</i></p>

<p>32 hashes to location 2. Because 32 is not equal to the element at index 2, the delete routine begins traversing the linked list. 32 is found at the node one after the head of the linked list. The head pointer is reconfigured to point to the element after 32. The node containing 32 is deleted from the list. </p>


<p><b>Linear Probing Table:</b><i>right table</i></p>

<p>32 hashes to index 2. Because 32 is not equal to the element at index 2, the delete routine linearly searches for the element unless encountering a blank spot at any time. This event is a clear sign that the element is not present in the data structure. In this case, the routine searches index 2 and then index 3 where element 32 happens to resides. The element is then deleted. However, the answer on how to do this is not so simple. Deleting this element creates a blank spot which interferes with the decision making of all other algorithms on the Hash Table designed to quit when encountering a blank spot. A search operation might never find 149 if we don't perform a swap or a "lazy deletion." More information is given in links below. </p>
</div>


<p> Time Analysis of an arbitrary <b>Delete(x)</b> operation on both tables: </p>
<div style="font-size:80%" id="insert_time_analysis" > 
<p><b>Chained Hash Table:</b><i>left table</i></p>
<p>Consider an input size of n elements. If all n elements map to the same location (e.g. a bad hash function like H(x) = <i>a</i> where <i>a</i> is a fixed constant), a linked list that is n elements long is built. In the worst case, the element needing to be deleted is the last element in the n-size linked list. Deleting this tail element takes O(n) time. Implementation Note: the routine delete must be able to properly handle linked list deletion as well as deal with situations where the head of the linked list requires deletion.</p>

<p><b>Linear Probing Table:</b><i>right table</i></p>
<p>Consider an input size of n elements. If all n elements map to the same location (e.g. a bad hash function like H(x) = <i>a</i> where <i>a</i> is a fixed constant), elements are inserted linearly apart from index <i>a</i>. This would result in primary clustering at index a. If the element-to-be-deleted is the element last inserted, the delete routine has to probe n times. A fair average case is O(1), since clustering is usually only a few elements large. When deleting though, leaving blank spots interferes with search operations. Read here for more info on how to overcome this: http://en.wikipedia.org/wiki/Lazy_deletion.</p>

<p><b>Note</b>: delete complexity for both tables can be dramatically reduced by simply doubling the capacity of the Hash Table. This allows for a greater number of unique indexes and thus less chance of collision or clustering. </p>
</div>

</div>
