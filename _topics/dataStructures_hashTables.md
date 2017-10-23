---
topic: "Data Structures: Hash Tables"
desc: "Open and Closed Hashing, etc."
---

All hash tables have in common the idea that a hash function $$H(x)$$, maps a value to a location.   For simplicity, we'll treat this location as the "index of an array of size n", i.e. an integer value between 0 and n-1.

This scheme works perfectly when there are no hash collisions.
* A hash collision happens when $$x_1\ne x_2$$, but  $$H(x_1)=H(x_2)$$.
* Example: For $$H(x)$$=`x%100`, $$H(201)=H(401)$$

The best way to deal with this is to choose a clever hash function&mdash;one that takes the statistical distribution of your data into account, and choose a scheme that makes collisions very unlikely.     

But, since the number of data values you have is usually much larger than the size of your array, by the Pigeonhole Principle (which you learned about, or will learn about in CS40), there will ALWAYS be some collisions.  What to do?   

There are two basic approaches, with some variations on each. Sadly, the terminology used for these techniques can be quite confusing.

One way of thinking about the two approaches:
* Does the array store objects themselves&mdash;in this case, when there's a collision you go "probing" for the value, i.e. iterating through the table until you are satisfied you can't find the item.  The most basic approach, <em>linear probing</em>, just iterates by 1 (with wrap around) until an empty slot is found.   Variations on that use more elaborate ways of skipping from one item to the next.
* Does the array store a pointer to a data structure containing the items that hash to a particular key&mdash;e.g. a linked list of those items.

The terminology can gets confusing here.   

| How it works | Hashing&mdash;do we leave the hash table, or not, to find data? | Addressing&mdash;how do we determine an address? | Other names that may apply |
|--|--|--|--|
| All elements are in the array, and you go probing through the array to resolve collisions. | Closed Hashing: nothing is outside the hash table | Open Addressing: the address is not determined only by the key, but also by what else is in the hash table | There are various kinds of probing: linear probing, quadratic probing, double hashing |
| The element are outside the array; each array element is the head of a linked list (or other data structure) | Open Hashing: we may have to go outside the hash table to find our data | Closed Addressing: the hash function tells us exactly where to go (though we may have to iterate through a list once we get there. | <b>Separate Chaining</b> is the term often used for this technique. |


# The Underlying Idea 

Below is the a summary of the three most common operations supported by a Hash Table. Each operation is listed with its average case and its worst case complexity.

<div style="text-indent: 50px; font-size:130%" id="ADT">
<table>
  <tr>
     <td>
       <p markdown="1"> `find(item)` </p> 
    </td>
    <td>
       <p markdown="1"> $$O(1)$$ in average case </p>
     </td>
    <td>
       <p markdown="1"> $$O(n)$$ in worst case </p>
    </td>
  </tr>
  <tr>
     <td>
       <p markdown="1"> `insert(item)` </p>
    </td>
    <td>
       <p markdown="1"> $$O(1)$$ in average case </p>
     </td>
     <td>
       <p markdown="1"> $$O(n)$$ in worst case </p>
     </td>
  </tr>
  <tr> 
     <td>
       <p markdown="1"> `delete(item)` </p>
    </td>
    <td>
       <p markdown="1"> $$O(1)$$ in average case </p>
     </td>
     <td>
       <p markdown="1"> $$O(n)$$ in worst case </p>
     </td>
  </tr>
</table>
</div> 

Hash Tables are a data structure that take advantage of <b>random access</b> to achieve extremely fast lookups. "Random Access" in this case is a term of art that means we can directly access a piece of data at any arbitary address in memory (i.e. RAM).     The term RAM for memory refers to this property&mdash;it is an acronym for "random access memory".

An array in C++, for example, supports random access: you can index into an array by adding an offset (computed by the array index) to the base address, and directly retrieve any element in one operation.




