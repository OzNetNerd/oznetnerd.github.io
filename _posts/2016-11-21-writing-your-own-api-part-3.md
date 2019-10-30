---
title: Writing your own API, Part 3
sub_heading: "Retrieve structured data from your network devices"
redirect_from: /writing-your-own-api-part-3/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- TextFSM
- Python

tags:
- Ansible
- Automation
- TextFSM
- DevOps
---
In **[my previous post](/writing-your-own-api-part-2/)** I covered how to to download, install and test TextFSM. In this post I'll demonstrate how to write your own template files so that you can create (and hopefully contribute!) them if a suitable one does not already exist.

It is worth mention that the examples in this post are extremely basic. I've done it this way on purpose because I'm only trying to demonstrate how to use TextFSM, not how to write regex. I feel that trying to do both in a single post would cause unnecessary confusion for those who are not familiar with either.

Note: I recommend you read the [**TextFSM**](https://github.com/google/textfsm/wiki/TextFSM) and [**Code Lab**](https://github.com/google/textfsm/wiki/Code-Lab) documentation  before proceeding with this post.

## Example #1

Let's create a text file called **text_example** and enter the following lines:

```
This is the first test we are running: one
This is the second test we are running: two
This is the third test we are running: three
```

Now let's say we want TextFSM to match the highlighted bits of text. (The reason why I have used two different colours is because we'll need to store them in two separate variables.)

Now let's create a template file called **text_template** and enter the following lines:

```
Value Match1 (\w+)
Value Match2 (\w+)

Start
 ^This is the ${Match1} test we are running: ${Match2}
```

Here we see some basic regex being used to match the first bit of text (the words first, second and third) and store them in a variable called Match1, as well as the second bit of text (the words one, two and three) and store them in a variable called Match2.

Let's run our code and see what happens:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)

Start
 ^This is the ${Match1} test we are running: ${Match2}


FSM Table:
['Match1', 'Match2']
['third', 'three']
```

This doesn't look right - We're only seeing the third line of our **text_exmaple** file.  Any ideas why this might have happened? I'll tell you - it is because we need to use the Record action.

As a result of this, when we run the code TextFSM stores the values **first** and **one**, but as there was no Record action, TextFSM it continues onto the next line of the **text_exmaple** file without saving these values.

Putting it visually, the TextFSM table would look like this at this point:

||||||
|--- |--- |--- |--- |--- |
|Line:|Match1:|Match2:|Status:|Row:|
|This is the first test we are running: one|first|one|N/A|1|

TextFSM then proceeds to the next line of the **text_example** file and finds that **second** and **two** match the **Match1** and **Match2** variables' regex and therefore overwrites the **first** and **one** values.

||||||
|--- |--- |--- |--- |--- |
|Line:|Match1:|Match2:|Status:|Row:|
|This is the second test we are running: two|first
second|one
two|N/A|1|

However, as already mentioned above, there was no Record action and therefore TextFSM continued onto the third line of the **text_example** file and found **third** and **three**. Again, as these values matched the **Match1** and **Match2** variables' regex, the new values overrode the previously stored values.

As TextFSM has now reached the end of the file (EOF), it automatically  implements a Record action and therefore the **third** and **three** values are stored.

||||||
|--- |--- |--- |--- |--- |
|Line:|Match1:|Match2:|Status:|Row:|
|This is the third test we are running: three|second
third|two
three|Recorded|1|

To resolve this issue, we simply need to implement the Record action, as highlighted below:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)

Start
 ^This is the ${Match1} test we are running: ${Match2} -> Record

FSM Table:
['Match1', 'Match2']
['first', 'one']
['second', 'two']
['third', 'three']
```

The resulting table now looks like this:

||||||
|--- |--- |--- |--- |--- |
|Line:|Match1:|Match2:|Status:|Row:|
|This is the first test we are running: one|first|one|Recorded|1|
|This is the second test we are running: two|second|two|Recorded|2|
|This is the third test we are running: three|third|three|Recorded|3|

## Example #2

It is important to note that each time TextFSM reads a new line of text from our **text_example** file, it tries to match that line with the regex stored in our **text_template** file in a top down fashion (similar to the way in which an ACL works).

For example, let's see what happens when we add a couple of new lines to our our **text_template** file:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)
Value Match3 (\w+)

Start
 ^This is the ${Match1} test we are running: ${Match2} -> Record
 ^This is the second test we are running: ${Match3} -> Record

FSM Table:
['Match1', 'Match2', 'Match3']
['first', 'one', '']
['second', 'two', '']
['third', 'three', '']
```

We see that although our new line matches the second line of the **text_example** file, the value is not stored in the FSM table. This is because all three of the **text_example** lines are matched by the first line of the **template_example** file, and, just like an ACL, as soon as a match is found TextFSM stops checking the template file for matches.

To resolve this issue we would need to move the newly added **template_example** line above the original line, like so:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)
Value Match3 (\w+)

Start
 ^This is the second test we are running: ${Match3} -> Record
 ^This is the ${Match1} test we are running: ${Match2} -> Record


FSM Table:
['Match1', 'Match2', 'Match3']
['first', 'one', '']
['', '', 'two']
['third', 'three', '']
```

## Example #3

The previous example brings me to another point - in that example we saw two Record actions used in **template_example**. This is perfectly legal and simply means that if either of the two corresponding regex match, record the variables.

This is very handy when you've got a situation where the same pieces of information are being displayed in two different styles.

For example, let's change the contents of the **text_example** file to this:

```
This is the first test we are running: one
This is the second test we are running: two
The number is: three and this is the third test we are running
```

Note that the last line has changed and that I've reversed the positions of the words **three** and **third**. As a result of this, we'll need to update our **template_example** file to match this new line as well as ensure that the **Match1** and **Match2** variables match the correct pieces of text:

```
  ^The number is: ${Match2} and this is the ${Match1} test -> Record
```

_Note that the above regex ends with the word **test**, however, the full line in our **text_example** file ends with the words **test we are running**. This is perfectly fine and will still match because that is simply the way regex works - you do not need to match the entire line._

Let's now run the code and see what happens:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)

Start
 ^The number is: ${Match2} and this is the ${Match1} test -> Record
 ^This is the ${Match1} test we are running: ${Match2} -> Record


FSM Table:
['Match1', 'Match2']
['first', 'one']
['second', 'two']
['third', 'three']
```

There are two things to note here - the first being that our **third** and **three** variables were stored in the correct order as highlighted above.

The second thing being that we added the new line of regex on top of the original line in our **test_template** file. This is fine because as mentioned previously, TextFSM will work from the top of the **test_template** file and continue going down until it finds a match. This means that the first and second lines of the **test_example** file were matched by the second line of the **test_template** file, and the third line of the **test_example** was matched by the first line of the **test_template** file.

Just like an ACL, you should have your most specific matches at the top of the **template** list and your more broad matches at the bottom.

## Example #4

Previously I mentioned that regex does not need to match the entire line. Well, another interesting thing to note is that it doesn't need to match information you're not interested in either. To demonstrate this, let's change the **test_example** file to the following:

```
This is the first test we are running: one
Garbage text
Garbage text
Garbage text
This is the second test we are running: two
More grabage
More grabage
More grabage
This is the third test we are running: three
Even more garbage!!!
Even more garbage!!!
Even more garbage!!!
```

Now let's run the code and see what happens:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)

Start
 ^This is the ${Match1} test we are running: ${Match2} -> Record

FSM Table:
['Match1', 'Match2']
['first', 'one']
['second', 'two']
['third', 'three']
```

All of our text matches as it did before the garbage text was added. This is because TextFSM simply ignores lines which do not match its regex.

## Example #5

Note that as per the TextFSM documentation (linked to at the top of this post), the Start state is always required. Without it, you cannot run your TextFSM code.

Further to this, you can choose to add additional states and use redirections between them in order to keep your code clean. For example, let's change the contents of **test_example** to include two additional lines:

```
This is the first test we are running: one
This is the second test we are running: two
Test 3 requires extra processing
Store this new value too: Extra
This is the third test we are running: three
This is the fourth test we are running: four
```

Let's also update our **test_template** file to match this:

```
Value Match1 (\w+)
Value Match2 (\w+)
Value Match3 (\w+)

Start
 ^.*extra processing -> ExtraProc
 ^This is the ${Match1} test we are running: ${Match2} -> Record

ExtraProc
 ^Store this fourth value too: ${Match3}
```

What we're trying to do is redirect to the **ExtraProc** state when the text **extra processing** is found in the **test_example** file. Once there, we want to store the word **Extra**.

Let's run the code and see what happens:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)
Value Match3 (\w+)

Start
 ^.*extra processing -> ExtraProc
 ^This is the ${Match1} test we are running: ${Match2} -> Record

ExtraProc
 ^Store this new value too: ${Match3}


FSM Table:
['Match1', 'Match2', 'Match3']
['first', 'one', '']
['second', 'two', '']
['', '', 'Extra']
```

We can see that the **Extra** value was recorded, but we're missing the **third**, **three**, **fourth** and **four** values. Given that **Extra** was stored it tells us that the redirection to **ExtraProc** worked. Further to this, as the other values are missing it tells us that the redirection back to the **Start** did not work. After reviewing our code we can see that it didn't work because it isn't there!

Let's add it and rerun our code:

```
FSM Template:
Value Match1 (\w+)
Value Match2 (\w+)
Value Match3 (\w+)

Start
 ^.*extra processing -> ExtraProc
 ^This is the ${Match1} test we are running: ${Match2} -> Record

ExtraProc
 ^Store this new value too: ${Match3} -> Start


FSM Table:
['Match1', 'Match2', 'Match3']
['first', 'one', '']
['second', 'two', '']
['third', 'three', 'Extra']
['fourth', 'four', '']
```

Success!

Just to be clear, the above code does the following:

1.  Find a match in the **Start** state and redirecting it to the **ExtraProc** state.
2.  Next, find a match in the **ExtraProc** state and store the required information in a variable.
3.  We're then redirecting back to the **Start** state and storing the additional information we need in variables.
4.  We're then **Recording** that information.

Note that lines which do not meet the **ExtraProc** regex in the **Start** state are not redirected to the **ExtraProc** state and are Recorded immediately instead. In other words, steps 1 and 2 above are skipped.