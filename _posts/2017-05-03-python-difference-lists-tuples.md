---
title: 'Python: The difference between Lists & Tuples'
sub_heading: "They're the same, but different"
redirect_from: /python-difference-lists-tuples/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Python

tags:
- Python
- Coding
- DevOps
---
A common misconception is that the **only** difference between Lists and **[Tuples](http://en.wikipedia.org/wiki/Tuple "Tuple")** is that the former is mutable while the latter is immutable. While this is true, it is only **one **of the differences between them as per the Q&As quoted below.

## Question #1

[**What's the difference between lists and tuples?**](http://stackoverflow.com/questions/626759/whats-the-difference-between-lists-and-tuples)

### Answer #1

_[**(Answer link)**](http://stackoverflow.com/a/36156178/6233477)_

_... This gives rise to differences in how they can or can't be used (enforced a priori by syntax) and differences in **how people choose to use them** (encouraged as 'best practices,' a posteriori, this is what smart programers do). The main difference a posteriori in differentiating when tuples are used versus when lists are used lies in **what meaning people give to the order of elements**._

_For tuples, 'order' signifies **nothing more** than just a specific 'structure' for holding information. What values are found in the first field can easily be switched into the second field as each provides values across two different dimensions or scales. They provide answers to different types of questions and are typically of the form: for a given object/subject, what are its attributes? The object/subject stays constant, the attributes differ._

_For lists, 'order' signifies a **sequence** or a directionality. The second element **MUST** come after the first element because it's positioned in the 2nd place based on a particular and common scale or dimension. The elements are taken as a whole and mostly provide answers to a single question typically of the form, for a given attribute, how do these objects/subjects compare? The attribute stays constant, the object/subject differs._

_There are countless examples of people in popular culture and programmers **who don't conform to these differences** and there are countless people who might use a salad fork for their main course. At the end of the day, it's fine and both can usually get the job done._

**To summarize some of the finer details**

**Similarities:**

1.  **_Duplicates_** - Both tuples and lists allow for duplicates
2.  **_Indexing, Selecting, & Slicing_** - Both tuples and lists index using integer values found within brackets. So, if you want the first 3 values of a given list or tuple, the syntax would be the same:
    
    ```python
    >>> my_list[0:3]
    [0,1,2]
    >>> my_tuple[0:3]
    [a,b,c]
    ```
    
3.  **_Comparing & Sorting_** - Two tuples or two lists are both compared by their first element, and if there is a tie, then by the second element, and so on. No further attention is paid to subsequent elements after earlier elements show a difference.
    
    ```python
    >>> [0,2,0,0,0,0]>[0,0,0,0,0,500]
    True
    >>> (0,2,0,0,0,0)>(0,0,0,0,0,500)
    True
    ```

**Differences: - A priori, by definition**

1.  **_Syntax_** - Lists use [] , tuples use ()
2.  **_Mutability_** - Elements in a given list are mutable, elements in a given tuple are **NOT** mutable.
    
    ```python
    # Lists are mutable:
    >>> top_rock_list
    ['Bohemian Rhapsody', 'Kashmir', 'Sweet Emotion', 'Fortunate Son']
    >>> top_rock_list[1]
    'Kashmir'
    >>> top_rock_list[1] = "Stairway to Heaven"
    >>> top_rock_list
    ['Bohemian Rhapsody', 'Stairway to Heaven', 'Sweet Emotion', 'Fortunate Son']
    
    # Tuples are NOT mutable:       
    >>> celebrity_tuple
    ('John', 'Wayne', 90210, 'Actor', 'Male', 'Dead')
    >>> celebrity_tuple[5]
    'Dead'
    >>> celebrity_tuple[5]="Alive"
    Traceback (most recent call last):
    File "", line 1, in TypeError: 'tuple' object does not support item assignment
    ``` 
    
3.  **_Hashtables (Dictionaries)_** - As hashtables (dictionaries) require that its keys are hashable and therefore **immutable**, only tuples can act as dictionary keys, **not lists**.
    
    ```python
    #Lists CAN'T act as keys for hashtables(dictionaries)
    >>> my_dict = {[a,b,c]:"some value"}
    Traceback (most recent call last):
    File "", line 1, in TypeError: unhashable type: 'list'
    
    #Tuples CAN act as keys for hashtables(dictionaries)
    >>> my_dict = {("John","Wayne"): 90210}
    >>> my_dict
    {('John', 'Wayne'): 90210}
    ``` 

**Differences - A posteriori, in usage**

1.  **Homo vs. Heterogeneity of Elements** - Generally list objects are homogenous and tuple objects are heterogeneous. That is, lists are used for objects/subjects of the same type (like all presidential candidates, or all songs, or all runners) whereas although it's not forced by), whereas tuples are more for heterogenous objects.
2.  **Looping vs. Structures** - Although both allow for looping (for x in my_list... ), it only really makes sense to do it for a list. Tuples are more appropriate for structuring and presenting information (%s %s residing in %s is an %s and presently %s % ("John","Wayne",90210, "Actor","Dead"))

### Answer #2

_[**(Answer link)**](http://stackoverflow.com/a/626871/6233477)_

_Apart from tuples being immutable there is also a semantic distinction that should guide their usage. Tuples are heterogeneous data structures (i.e., their entries have different meanings), while lists are homogeneous sequences. **Tuples have structure, lists have order.**_

_Using this distinction makes code **more explicit and understandable.**_

_One example would be pairs of page and line number to reference locations in a book, e.g.:_

```python
my_location = (42, 11) # page number, line number
```

_You can then use this as a key in a dictionary to store notes on locations. A list on the other hand could be used to store multiple locations. Naturally one might want to add or remove locations from the list, so it makes sense that lists are mutable. On the other hand it doesn't make sense to add or remove items from an existing location - hence tuples are immutable._

_There might be situations where you want to change items within an existing location tuple, for example when iterating through the lines of a page. But tuple immutability forces you to create a new location tuple for each new value. This seems inconvenient on the face of it, but using immutable data like this is a cornerstone of value types and functional programming techniques, which can have substantial advantages._

_There are some interesting articles on this issue, e.g. **["Python Tuples are Not Just Constant Lists"](http://jtauber.com/blog/2006/04/15/python_tuples_are_not_just_constant_lists/)** or **["Understanding tuples vs. lists in Python"](http://news.e-scribe.com/397)**. The official Python documentation **[also mentions this](http://docs.python.org/2/tutorial/datastructures.html#tuples-and-sequences)** ("Tuples are immutable, and usually contain an heterogeneous sequence ...")._

_In a statically typed language like Haskell the values in a tuple generally have different types and the length of the tuple must be fixed. In a list the values all have the same type and the length is not fixed. So the difference is very obvious._

_Finally there is the **[namedtuple](http://docs.python.org/dev/library/collections.html#collections.namedtuple)** in Python, which makes sense because a tuple is already supposed to have structure. This underlines the idea that tuples are a light-weight alternative to classes and instances._

## Question #2

[**Why is tuple faster than list?**](http://stackoverflow.com/questions/3340539/why-is-tuple-faster-than-list)

### Answer #1

_[**(Answer link)**](http://stackoverflow.com/a/3340881/6233477)_

_A tuple made out of constant literals can easily be identified by the Python compiler as being one, immutable constant literal itself: so it's essentially built just once, when the compiler turns the source into bytecodes, and stashed away in the "constants table" of the relevant function or module. When those bytecodes execute, they just need to recover the pre-built constant tuple -- hey presto!-)_

_This easy optimization cannot be applied to lists, because a list is a mutable object, so it's crucial that, if the same expression such as_ [1, 2, 3] _executes twice (in a loop -- the_ timeit  _module makes the loop on your behalf;-), a fresh new list object is constructed anew each time -- and that construction (like the construction of a tuple when the compiler cannot trivially identify it as a compile-time constant and immutable object) does take a little while._

_That being said, tuple construction (when both constructions actually have to occur) still is about twice as fast as list construction -- and that discrepancy can be explained by the tuple's sheer simplicity, which other answers have mentioned repeatedly. But, that simplicity does not account for a speedup of six times or more, as you observe if you only compare the construction of lists and tuples with simple constant literals as their items!_)_

### Answer #2

_[**(Answer link)**](http://stackoverflow.com/a/3341629/6233477)_

_Alex gave a great answer, but I'm going to try to expand on a few things I think worth mentioning. Any performance differences are generally small and implementation specific: so don't bet the farm on them._

_In CPython, tuples are stored in a single block of memory, so creating a new tuple involves at worst a single call to allocate memory. Lists are allocated in two blocks: the fixed one with all the Python object information and a variable sized block for the data. That's part of the reason why creating a tuple is faster, but it probably also explains the slight difference in indexing speed as there is one fewer pointer to follow._

_There are also optimisations in CPython to reduce memory allocations: de-allocated list objects are saved on a free list so they can be reused, but allocating a non-empty list still requires a memory allocation for the data. Tuples are saved on 20 free lists for different sized tuples so allocating a small tuple will often not require any memory allocation calls at all._

_Optimisations like this are helpful in practice, but they may also make it risky to depend too much on the results of 'timeit' and of course are completely different if you move to something like IronPython where memory allocation works quite differently_

## Conclusion

To sum up the above, we've found is that:

*   Lists are mutable, tuples are not.
*   Both are ordered.
*   Both can be indexed, sliced and compared.
*   Tuples are hashable, lists are not.
*   List objects are homogenous while tuple objects are heterogeneous
*   Although both allow for looping ( for x in my_list... ), it only really makes sense to do it for a list. Tuples are more appropriate for structuring and presenting information.
*   Tuples are much quicker than lists in terms of processing time.