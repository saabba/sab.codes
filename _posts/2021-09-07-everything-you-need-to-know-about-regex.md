---
layout: default
title:  "Everything You Need to Know About Regex (WIP)"
day: 7th
month: September
year: 2021
author: sabrina
category: [Toolbox]
---

Regex is amazing, super powerful, and is a topic that every programmer need to be familiar with. Regex is short for regular expressions, and it is a string that describes a pattern to be found in other pieces of text.

## What is Regex Used For?

1. Finding
2. Replacing
3. Validating
4. Splitting

## How Do We Use Regex?

Regex is ran using a regex engine. A regex engine has support for finding, replacing, validating, and splitting. Many programs you are already familiar for support regex in their search/find functionality including but not limited to the following:
- Notepad ++
- Visual Studio

Also, most programming languages support regex including C#, Python, and JavaScript! There are a few advanced regex topics that aren’t supported everywhere, but most of the basics are the same in every regex engine.

My favorite regex engine to use for writing and testing regex before implementing in my final code is [RegExr](https://regexr.com/). [RegExr](https://regexr.com/) has a helpful reference list and you can visually see your matches as you make changes to you regex. You can even replace values to see what your final string will look like if you are using regex for replacements.

## The Basics

### Matching Characters

We will start by just matching a single word in a sentence to learn some of the basics.
Let’s use the sentence ***Where are we going to get food?***
And let’s find the word ***food***. This regex match is as easy. It is just as easy as doing a case-sensitive search in any program you are familiar with. To find the word food in the sentence, the regex is just ```food```. ```Food``` will not work since regex is case sensitive.
There are some characters that cannot be used in regex without an escape character since they have special meanings in regex. The escape character in regex is ***\\*** . Here are the common escaped characters

| Description                             | Example  |
|-----------------------------------------|----------|
| **Reserved Characters**<br />+*?^$\\.[]{}()\|/ | \\+       |
| **Octal Escape**                            | \000     |
| **Hexadecimal Escape**                      | \xFF     |
| **Unicode Escape**                          | \uFFFF   |
| **Extended Unicode Escape**                 | \u{FFFF} |
| **Control Character Escape**                | \cI      |
| **Tab**                                     | \t       |
| **Line Feed**                               | \n       |
| **Vertical Tab**                            | \v       |
| **Form Feed**                               | \f       |
| **Carriage Return**                         | \r       |
| **Null**                                    | \0       |

### Matching Characters Using Quantifiers

In the word ***food***, there are 2 **o**s. Do we have to type out the word to match or is there a different way to do this matching? You guessed it. There is a different way! Now, matching the word food in a real scenario it would be best to just use the simplest most readable regex from above, ```food```, but for examples and build on the basics here are other ways the match can be written, ```fo*d``` or ```fo+d```. The ***\**** means match 0 or more of the previous character and ***+*** means match 1 or more of the previous character. So what happens if there was a typo in the text we were searching and food had 3 **o**s? Well both of those would match the word foood as well which is not what we want. We can however use regex to say match on 2 occurrences exactly using ```fo{2}d```. 
Here are some other useful quantifiers that we can go into detail in later examples.

| Name                                                                                                          | Ex    |
|---------------------------------------------------------------------------------------------------------------|-------|
| **plus**<br/>Matches 1 or more of the preceding token                                                             | +     |
| **star**<br/>Matches 0 or more of the preceding token                                                             | *     |
| **quantifier**<br/>Matches the specified quantity of the previous token                                           | {1,3} |
| **optional**<br/>Matches 0 or 1 of the preceding token                                                            | ?     |
| **lazy**<br/>Matches as few characters as possible. By default matches will match as many characters as possible. | ?     |
| **alternation**<br/>Acts the the boolean OR. Matches one or the other. Can be used in groups.                     | \|    |

### Matching Characters Using Character Groups

Now let’s use the sentence ***Where are we going to get food to feed Max?*** , and the goal of matching 2 words, ***food*** and ***feed***. These words are very similar. The only difference are the middle 2 letter, ***o*** and ***e***.
To do this, let’s use a character group. Insead of matching just an ***o*** we also want to match an ***e***. That can be written using square brackets, ***[]*** , with an ***o*** and ***e*** inside, ```[oe]```. That regex matches an ***o*** or ***e***. If we swap that into the regex we wrote before, we get ```f[oe]{2}d``` which matches both ***food*** and ***feed***. We could also write this using alternation, ```f(oo|ee)d``` or ```f(o|e){2}d``` or many other ways.

### Using Capturing Groups

Now that we know some basic matching techniques, let’s learn about capturing groups. Capturing groups come in handy when you want to replace data in a string or use a specific part of the match for another reason. Let’s start with a new example, say a list of names and email addresses. We need a tab delimited file with name in column 1 and phone number in column 2, but the phone number formatting is all over the place currently.

#### Example Data to Clean

```
Sally   2344447890
Joe		123-456 9999
Susan 	(789)999-9999
Samantha	888-888-8888
Max	  	 234.123.1234
```

#### Matching Regex

```
([a-zA-Z]+)\s+\D*([0-9]{3})\D*([0-9]{3})\D*([0-9]{4})
```

#### List Data

```
$1\t$2-$3-$4\n
```

## References

[RegExr: Learn, Build, & Test RegEx](https://regexr.com/)

