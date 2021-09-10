---
layout: default
title:  "Everything You Need to Know About Regex"
day: 7th
month: September
year: 2021
author: sabrina
category: [Toolbox]
---
This is still a WIP
## What is Regex?
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
Let’s use the sentence ```Where are we going to get food?```
And let’s find the word ```food```
This regex match is as easy. It is just as easy as doing a case-sensitive search in any program you are familiar with. To find the word food in the sentence, the regex is just ```food```. ```Food``` will not work since regex is case sensitive.
There are some characters that cannot be used in regex without an escape character since they have special meanings in regex. The escape character in regex is \ . Here are the common escaped characters

