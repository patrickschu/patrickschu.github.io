# String processing in Python
- [String processing in Python](#string-processing-in-python)
    - [1. Reading a text file](#reading-a-text-file)
    - [2. Basic string operations: Splitting text, cleaning up text/whitespace, identifying punctuation](#basic-string-operations-splitting-text-cleaning-up-textwhitespace-identifying-punctuation)
    - [3. Basic text processing: tokenization](#basic-text-processing-tokenization)
    - [4. String interpolation: using variables in strings](#string-interpolation-using-variables-in-strings)
    - [5. Escaping characters](#escaping-characters)
    - [6. Regular Expressions](#regular-expressions)
    - [Appendix: String processing gotchas](#appendix-string-processing-gotchas)



This is a very basic introduction to string processing in Python. 
It addresses usage in Python 3 only - see [here](https://www.pitt.edu/~naraehan/python2/unicode.html) for a guide to string processing in Python 2.

This guide focuses on standard text processing tasks such as reading files and processing text.

It might be be useful for people newer to Python who are working with documents, social media posts, or other text input. 

### 1. Reading a text file

The below example illustrates some best practices. 

```
# reading the file sample_file_txt
with open("sample_file.txt", "r", encoding="utf-8") as filein:
	text = filein.read() 
```

This snippet shows:

1. File opening should happen using a [context manager](https://www.python.org/dev/peps/pep-0343/), i.e. `with open() as`. 
This takes care of hassles such as opening and closing files properly, and erroring out as orderly as possible. 
The context manager helps avoid issues with missing or corrupted data in files, which can be really hard to debug.

2. An encoding is specified in the [open()](https://docs.python.org/3/library/functions.html#open) command.
This is not a required argument to `open()` , but people seem  to agree that it kind of should be.
The problem is if you do not set it, the Python will use different encoding schemes based on the Operating System the code is run on: the `encoding` argument defaults to  "cpb1525" on  Windows, and "utf-8" on any other machine. 
In other words, your code will *behave differently depending on which machine it is run*. 
Even if you are "only using ASCII anyway", note that plenty of characters — including the dash that started this phrase, or the “ quotation marks — will lead to your code erroring out on Windows. 

In 99% of cases, just set the encoding to "utf-8", unless you have very  well-informed reasons to do otherwise. 
Note that the same advice is also given by well-regarded Python experts such as Luciano Ramalho in [Fluent Python](https://www.oreilly.com/library/view/fluent-python-2nd/9781492056348/). 

### 2. Basic string operations: Splitting text, cleaning up text/whitespace, identifying punctuation

__Splitting text__: Python has a very simple [`split`()](https://docs.python.org/3.3/library/stdtypes.html?highlight=split#str.split) string method. 
Use it e.g. to split a text on whitespace: "Hello friends".split(). For more sophisticated string splitting, use the [`re.split()`]( https://docs.python.org/3/library/re.html#re.split) function in the in-built regular expression module *re*.  

E.g. to split on any non-alphanumeric character:
```
import re
re.split("\W+", "The user-name is Timmy! (Not `Tommy`)...")
# ['The', 'user', 'name', 'is', 'Timmy', 'Not', 'Tommy', '']
```

__Cleaning up text, e.g. removing whitespace__: Python has a [strip()](https://docs.python.org/3/library/stdtypes.html?highlight=strip#str.strip) string method. 
E.g. to strip surrounding whitespace from strings: 
```
# note that \n represents a line break
[i.strip() for i in ["This is Timmy:\n ", "    This is Tommy"]]
# ['This is Timmy:', 'This is Tommy']
```

When trying to strip various characters, Python strip() will remove each instance of either character - 
E.g. 
```
"Tommy".strip("my") 
# returns 'To'
"Tommy".strip("ym")
# returns 'To'
``` 

For more sophisticated string splitting, use the [`re.sub()`](https://docs.python.org/3/library/re.html#re.sub) function in the in-built regular expression module *re*.  

E.g. 
```
import re
re.sub("my", "", "Tommy")
# returns 'Tom'
```

3. Identifying punctuation and numbers: Python's in-built [string()](https://docs.python.org/3/library/string.html) module comes with pre-defined collections of characters. 

E.g. use string.punctuation  to identify common punctuation characters 
```
import string
[i for i in "What a world! Right?" if not i in string.punctuation]
# ['W', 'h', 'a', 't', ' ', 'a', ' ', 'w', 'o', 'r', 'l', 'd', ' ', 'R', 'i', 'g', 'h', 't']
```

Similar collections exist for numbers, alphanumeric characters, etc. 
Note that the string module does not cover unicode methods. The entire Unicode set is covered by Python's [unicodedata](https://docs.python.org/3/library/unicodedata.html) package. 


### 3. Basic text processing: tokenization

The most barebones, naive tokenization is the split on whitespace: 
```
"The user-name is Timmy! (Not `Tommy`)...".split()                                                                                                    
# ['The', 'user-name', 'is', 'Timmy!', '(Not', '`Tommy`)...']
```

The issues with this tokenization are obvious - punctuation sticks to the tokens, some punctuation such as hyphens is not treated appropriately, etc. 
A slightly more sophisticated tokenization can be achieved the inbuilt re module, as described above.

For any halfway serious attempt, Python packages such as [spacy](https://spacy.io/) or [NLTK](https://www.nltk.org/) offer much more performant and well-developed tokenizers. 


### 4. String interpolation: using variables in strings

String interpolation is the process of inserting variable pieces of text into another text. For example:

```
for user_name in ["Timi", "Tammi", "Tommy"]:
	print("User name is", user_name)

#User name is Timi
#User name is Tammi
#User name is Tommy
``` 

The above solution will work fine for printing random output to screen, but Python offers 3 approaches to more convenient string interpolation functions. All of the below will produce the exact same output as the above:

1. __f-string__: prefix string with an f, insert variable in curly brackets (note that this will not work in older versions of Python 3)
```
for user_name in ["Timi", "Tammi", "Tommy"]:
	print(f"User name is {user_name}")
``` 

2. __format string__: insert the variable into the strings `format()` method (this one offers a lot of formatting options, see [here](https://docs.python.org/3/library/string.html#formatstrings))

```
for user_name in ["Timi", "Tammi", "Tommy"]:
	print("User name is {}".format(user_name)) 
```

3. __% operator__: similar to other programming languages 
```
for user_name in ["Timi", "Tammi", "Tommy"]:
	print("User name is %s" %user_name) 
```

### 5. Escaping characters

Python uses the backslash \ character for escaping. E.g. to insert a quote mark into a string: `"He said \"Hello\" as he entered the room". Often times, these issues can be addressed by using single versus double quotes, or triple quotes, as well: 

```
'He said "Hello" as he entered the room.'
#He said "Hello" as he entered the room.
```

### 6. Regular Expressions

Regular expressions are important for searching through and editing strings. 
In Python, use the in-built [re](https://docs.python.org/3/library/re.html#module-re) module or the external [regex](https://pypi.org/project/regex/) module. 
The main advantage of the latter is that it includes fuzzy matching (i.e. edit distance).
Python's regular expression syntax is similar to the widely used Perl-style Regular expressions; see the official Python [guide](https://docs.python.org/3/library/re.html#regular-expression-syntax) and a useful cheat sheet [here](https://cheatography.com/davechild/cheat-sheets/regular-expressions/).

### Appendix: String processing gotchas

__Gotcha 1: Unicode code points v characters__

Note that in Unicode, characters that look identical on the the screen can be represented by different Unicode code points. 
For instance, the character *o* can be both [U+03BF](https://www.compart.com/en/unicode/U+03BF), the Greek Small Letter Omicron, as well as [U+006F](https://www.compart.com/en/unicode/U+006F), the Latin Small Letter O 
(see the official Unicode [confusables](https://util.unicode.org/UnicodeJsps/confusables.jsp) for more examples).

This can lead unexpected inequalities like the below

```
"ο" == "o" # returns False
# these character look-alikes are represented by different Unicode code points:
print("\u03BF")
print("\u006F")
```

Due to different ways of composing characters such as accented vowels, even length comparisons are not necessarily reliable. 


```
len('Rhône') == len('Rhône')
# False
# The first version is length 5, as expected; the second is length 6 since the '^' counts as a separate character in this instance.  
# Recreate this like so - note that the o circumflexe takes 2 characters in one version
print("Rh\u006F\u0302ne")
print("Rh\u00F4ne")
```

When dealing with larger sets of texts, [Unicode normalization](https://docs.python.org/3/library/unicodedata.html#unicodedata.normalize) is important to avoid issues like the above. 

__Gotcha 2: Python 2 v Python 3__ 

Most of the advice you find for string processing in Python 2, e.g. on Stackoverflow, does not translate to Python 3 at all. Strings were one of the major changes (and improvements) in Python 3. 


__Gotcha 3: Different quotation marks__ 

What quote marks should be used, e.g. `'STRING'` versus `"STRING"` versus `"""STRING"""`? It does not really matter.
