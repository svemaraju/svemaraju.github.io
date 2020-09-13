---
layout: post
title:  "5 Simple Recursion Programs in Python."
date:   2020-09-05 20:00:01 +0530
display_date: "Sep 05, 2020"
categories: python, programming
permalink: "/recursion-python"
---


Here are 5 simple Python recursive functions that will get you started with the practicing recursion.

These are exercise problems taken from the book - _Data Structures and Algorithms in Python Michael H. Goldwasser, Michael T. Goodrich, and Roberto Tamassia_


### Write a short recursive Python function that finds the minimum and maximum values in a sequence without using any loops.

The idea here is to split the list of numbers into two halves and recursively call the function on each of them and making a comparison until you have a list containing only one number in which case you just return the number.

```python
# Recursive algorithm for finding maximum number in 
# a array.
def maximum_in_list(nums):
    l = len(nums)
    if l == 1:
        return nums[0]
    m1 = maximum_in_list(nums[0:l//2])
    m2 = maximum_in_list(nums[l//2:l])
    if m1 > m2:
        return m1
    else:
        return m2
```

### Describe a recursive algorithm to compute the integer part of the base-two logarithm of n using only addition and integer division.

The idea here is to recursively call the function while dividing the number by 2. Returning 0 if the number is less than 2 or return 1 if the number is equal to 2.

```python
# Recursive algorithm to compute 
# integer part of base 2 logarithm of n.
def log_base2(n):
    if n < 2:
        return 0
    if n == 2:
        return 1
    return 1 + log_base2(n//2)
```

### Write a short recursive Python function that takes a character string and outputs its reverse.

This is a little hard to explain for me. The idea here is to get to the last character of the string by recursively calling the function where the 2 inputs are the first character of the string and the rest of the string. Once we get to the last character, we concatenate the inputs and return the result.

```python
# Recursive function to reverse a string.
def reverse_string(string):
    def get_reversed_string(string_a, string_b):
        ## check if string_b 
        ## is the last letter of the string.
        if len(string_b) == 1:
            return string_b + string_a
        else:
            return get_reversed_string(string_b[0], string_b[1:]) + string_a
    return get_reversed_string(string[0], string[1:])
```

### Write a short recursive Python function that determines if a string is a palindrome

The idea is pretty straightforward once you figured out how to reverse a string. 

```python
# Recursive function to check 
# if a string is a palindrome
def is_palindrome(string):
    if string == reverse_string(string):
        return True
    return False
```

### Use recursion to write a Python function for determining if a string s has more vowels than consonants.

The idea here is to split the string into two halves just like I did finding the maximum number in a list, in the very first program above. Once the string has only one character I will check whether it is a vowel or not, and thus the results get added up recursively.

```python
# Python function for determining if a 
# string has
# more vowels than consonants.
def if_more_vowels(string):
    def count_vowels(string):
        l = len(string)
        if l == 1:
            if string.lower() in ['a', 'e', 'i', 'o', 'u']:
                return 1
            return 0
        vowels_a = count_vowels(string[0:l//2])
        vowels_b = count_vowels(string[l//2:l])
        return vowels_a + vowels_b
    vowels = count_vowels(string)
    if vowels > len(string) - vowels:
        return True
    return False
```

Thanks for reading, let me know your thoughts or share your favorite recursive programs.

Happy learning.
