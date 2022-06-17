---
layout: post
author: Ethan Guyant
title:  Introduction to Functions
categories: Python
description: An introduction to python user-defined functions, their syntax, arguments, documentation, different types of functions, and much more.
---

### Naming and Syntax
A user defined function begins with the reserved keyword `def` followed by parentheses which inclose input parameters to the function and lastly a colon (`:`). Naming of these user-defined functions follow the same rules as the naming of variables. Including the name must not be a reserved keyword (e.g. `def`), must start with a letter or underscore, etc., as well as typically following certain conventions (e.g. lowercase_words_separated_with_underscores). For more details see [PEP 8 - Naming Conventions](https://www.python.org/dev/peps/pep-0008/#function-and-variable-names). The function body (i.e. all function statements) must be indented.

<br>

## General Notes
### DRY (Do Not Repeat) & Do One Thing
* When repeated code is identified it commonly indicates that a function should be utilized
* Each function should complete a single task
  * Large complex functions should typically be broken up into multiple smaller functions
  * Advantages:
    * Code will become more flexible
    * Easier to read and understand
    * Simplified debugging
    * Allow for easier updates

### Pass by Assignment
Also referred to by pass by object or pass by object reference. Information is passed to the function using 'pass by assignment'. See the below examples to better understand this.
```python
>>> a = [1,2,3]
>>> b = a
```
At this point both `a` and `b` point to the same object or location in memory
```python
>>> b is a
True
```
Since a list is a mutable object what happens to `b` will also happen to `a`
```python
>>> a.append(4)
>>> print(b)
[1, 2, 3, 4]
>>> b.append(5)
>>> print(a)
[1, 2, 3, 4, 5]
```
However if `a` is changed to a different object in memory, this does not change what object in memory `b` is pointing to. After this things that happen to `a` will no longer happen to `b`
```python
>>> a = 20
>>> a = a + 5
>>> print(a)
25
>>> print(b)
[1, 2, 3, 4, 5]
```
The above behavior of appending a value to the list `a` effecting the list `b` is due to a list being a mutable object. This behavior is not see if `a` and `b` are assigned to a immutable object.
```python
>>> a = 20
>>> b = a
>>> b is a
True
>>> b = b + 5
>>> b
25
>>> a
20
```
This is because `b = b + 5` assigns `b` to a different object in memory due to a `int` object being immutable. Immutable and mutable types are listed below:
* Immutable
  * int, float, bool, string, bytes, tuples, forzewnset, None
* Mutable
  * list, dict, set, bytearray, objects, functions, almost all other things

Due to this behavior mutable default arguments to a function should be avoided.
  * If a default mutable variable is required, defaulting to a value of `None` should be used and the variable can then be set within the function
    * More details below in the [Default Arguments](#default-arguments) section.

```python
>>> def broken_function(arg, var=[]):
	var.append(arg)
	print(var)

	
>>> broken_function('first call')
['first call']
```

Calling the function a single time returns the expected result. However, calling the function again shows the default value of `var` has been modified by the first call to the function

```python
>>> broken_function('second call')
['first call', 'second call']

# Improve the function by setting the the default argument to None
>>> def improved_function(arg, var=None):
	if var is None:
		var = []
	var.append(arg)
	print(var)

	
>>> improved_function('first call')
['first call']
>>> improved_function('second call')
['second call']
```
<br>

---

<br>

## Inclusion of Docstrings
In addition to the required syntax described above it is typically best practice to include a function docstring directly following the line of code which defines the function (`def function_name():`). Docstrings are string literals typically enclosed by triple quotes (either `'''` or `"""`) and are used to document the function. Enclosing the docstring in triple quotes allows for the documentation to expand more than a single line.

Helpful information to include in a docstring consists of:
* What the function does
* What the arguments of the function are
* What should be returned by the function
* Information regarding any errors the function may raise
* Any other relevant information about the function 

**At minimum the docstring should include what the function does and what it returns**

Docstrings should follow a consistent style. Two common styles include Google Style and Numpydoc style.

### Google Style
A Google Style docstring consists of:
* A concise description of what the function does
* An argument section if there are any arguments
  * If a given argument has a default value include "optional" when describing this argument
* A returns section that lists the expected type(s) of what the function returns
* Followed by any errors raises and extra notes

```python
def function(arg_1, arg_2=42):
    '''Description of what the function does.

    Args:
      arg_1 (str): Description of arg_1
      arg_2 (int, optional): Write optional when an argument has a default value

    Returns:
      bool: Optional description of the returned value

    Raises:
      ValueError: Include any error types that the function intentionally raises

    Notes:
      Any additional notes about the function
```

### Numpydoc Style
* Contains similar sections as the above described Google Style that is  formatted differently which can be easier to read, however tends to take up more vertical space
```python
  def function(arg_1, arg_2=42):
      '''
      Description of what the function does.

      Parameters
      ----------
      arg_1: str
        Description of arg_1.
      arg_2: int, optional
        Description of arg_2.
        Default=42
      
      Returns
      -------
      The type of the return value
        Can include a description of the return value.
        Replace "Returns" with "Yields" if the function is a generator
      '''
```


A function's docstring can be accessed with the `__doc__` attribute (unformatted docstring) or by calling the `help()` function and passing in the functions name (`help(function_name)`, which returns a formatted version of the docstring). For simple functions it may be more appropriate to have a simplified single line docstring in the form of "Do something, Return something". For more information see [PEP 257 - What is a Docstring](https://www.python.org/dev/peps/pep-0257/#what-is-a-docstring)

Simplified Docstring
```python
def raise_to_power(value1, value2):
    '''Raise value1 to the power of value2'''
    new_value = value1 ** value2
    return new_value
```
Google Style Docstring
```python
>>> def count_letter(content, letter):
     '''Count the number of times 'letter' appears in 'content'

        Args:
            content (str): The string to search.
            letter (str): The letter to search for.

        Returns:
            int
 
        Raises:
             ValueError: If 'letter' is not a one-character string.
        '''
        if (not isinstance(letter, str)) or len(letter) != 1:
            raise ValueError('`letter` must be a signle character string.')
        return len([char for char in content if char == letter])

>>> phrase = 'Every goal is possible from here'
>>> count_letter(phrase, 'e')
4
```
Get a Docstring using `__doc__`
```python
>>> count_letter.__doc__
    Count the number of times 'letter' appears in 'content'
    
    Args:
        content (str): The string to search.
        letter (str): The letter to search for.
        
    Returns:
        int

    Raises:
        ValueError: If 'letter' is not a one-character string.
```
Get Docstring using `inspect.getdoc()`
```python
>>> import inspect
>>> inspect.getdoc(count_letter)
Count the number of times 'letter' appears in 'content'

Args:
    content (str): The string to search.
    letter (str): The letter to search for.

Returns:
    int

Raises:
    ValueError: If 'letter' is not a one-character string.
```

>**`Note #1:`** Even functions that do not have arguments require empty parentheses `()`.

<br>

---

<br>

## Scope
There are different scopes that are present when running Python code.
* Global Scope: Defined in the main body of a script
* Local Scope: Defined inside of a function. Any parameter defined in a function only exists while the function executes
* Built-in: Names of pre-defined built-in functions and modules

In general scope determines which variables can be accessed in various parts of the code, and which variables can be accessed is determined by the following rules:
1. The Python interpreter looks first in the local scope
    * When within a function the local scope includes any arguments of, and variables defined, inside the function
2. If the variable cannot be found within the local scope, the interpreter searches the non-local scope (i.e. the parent function when a nested function is present)
3. If the variable cannot be found in the non-local scope, the interpreter searches the global scope (i.e. variables defined outside the function)
4. If the variable cannot be found in the global scope, the interpreter searches the built-in scope. The built-in scope includes things that are always accessible (e.g. `print()`)

When accessing a variable defined outside the current scope Python only grants read access
* Given the following example, when `x` is set to 50 within `func()` Python assumes the desired action is to create a new variable within the local scope

```python
>>> x = 10
>>> def func():
	x = 50
	print(x)
	
>>> func()
50
>>> print(x)
10

# Another Example
>>> s = 'global_scope'
>>> def print_value():
        s = 'local_scope'
        print(s)

# Print the value of n (outside of the function)
>>> print(s)
global_scope
# Call the function which prints the value of n (inside of the function)
>>> print_value()
local_scope
# Print the value of n (outside of the function) - The global n remains unchanged
>>> print(s)
global_scope
```
The second example above shows the following:   
1. The global variable `s` is assigned the value `global_scope`
2. Defines a function `print_value()` which assigns a local variable (or function parameter) `s` the value of `local_scope`
3. Prints the value of `s` outside of the function `print_value()` so it prints the string `global_scope`
4. Calls the function `print_value()`, which prints the value of the local variable `s`, `local_scope`
5. Again prints the value of `s` outside of the function which shows the the global variable `s` remains unchanged because the second assignment (`s = 'local_scope'`) impacts the local scope but does not impact the enclosing global scope

Rather, if the desired action is to change the value of `s` in the global scope (defined outside of the function) then this must be explicitly declared within the function using the keyword `global`
  * Modifying global variables in this manner should be used with caution as it can make debugging more challenging

```python
# Updating global variable inside of a function
>>> s = 'global_scope'
>>> def print_value():
        global s
        s = 'updated_inside_function'
        print(s)

# Print the value of n (outside of function) 
>>> print(s)
global_scope
# Call the function which prints the value of n (inside the function)
>>> print_value()
updated_inside_function
# Print the value of n (outside of the function)
>>> print(s)
updated_inside_function
```

Similarly, scopes must also be considered when nesting functions. To change the value of a variable in the non-local scope the `nonlocal` keyword must be used to modify the variable in the  enclosing scope.

```python
>>> def outer_func():
	x = 15
	def inner_func():
		x = 150
		print(x)
	inner_func()
	print(x)

>>> outer_func()
150
15

>>> def outer_func():
	x = 15
	def inner_func():
		nonlocal x
		x = 150
		print(x)
	inner_func()
	print(x)

>>> outer_func()
150
150
```
<br>

---

<br>

## Positional, Keyword, and Default Arguments
*Arguments* are the values passed to the function when it is called (`raise_to_power(3,2)`), `3` and `2` are the arguments passed to the function `raise_to_power`. The values of the arguments are copied to the corresponding *Parameters* (`value_1` and `value_2`) inside of the function.

The `return` statement returns the value of `new_value` back to the caller of the function.

Calling `raise_to_power` and passing in the values of `3` and `2` does the following:
```python
>>> def raise_to_power(value_1, value_2):
        '''Raises value_1 to the power of value_2'''

        new_value = value_1 ** value_2
        return new_value

>>> three_squared = raise_to_power(3,2)
>>> print(three_squared)
9
```
1) Assigns the value of `3` to the parameter `value_1` and `2` to the parameter `value_2` **inside** of the function.   
2) Performs the calculation `3` raised (`**`) to the power of `2`   
3) Returns the value `9` to the caller which then assigns it to the variable `three_squared`


Python handles function arguments in flexible manner. The most common types include positional arguments, keyword arguments, and default arguments.

<br>

### Positional Arguments
Positional argument values are copied to the function parameters in the order they are passed to the function.
```python
>>> def meals(breakfast, lunch, dinner):
        '''Builds a dictionary from the positional arguments and returns the dictionary'''

        return {'breakfast': breakfast, 'lunch': lunch, 'dinner': dinner}
 
>>> meals('toast', 'sandwich', 'tacos')
{'breakfast': 'toast', 'lunch': 'sandwich', 'dinner': 'tacos'}
```
Positional arguments are common, however carry the downside. Each time the function is called requires remembering the meaning and what value must go into what position. For example in the function above if `meals()` is called with tacos as the first argument the meals for the day would be very different.
```python
>>> meals('tacos', 'sandwich', 'toast')
{'breakfast': 'tacos', 'lunch': 'sandwich', 'dinner': 'toast'}
```

<br>

### Keyword Arguments
To avoid the potential confusion noted above, arguments can be passed to the function by specifying the arguments by the name of their corresponding function parameters.
```python
>>> meals(dinner='tacos', breakfast='toast', lunch='sandwich')
{'breakfast': 'toast', 'lunch': 'sandwich', 'dinner': 'tacos'}
```
When using keyword arguments the arguments do not have to be passed to the function in the same order that the parameters are defined within the function. 

Additionally a function can be called using both positional and keyword arguments.
```python
>>> meals('toast', dinner='tacos', lunch='sandwich')
{'breakfast': 'toast', 'lunch': 'sandwich', 'dinner': 'tacos'}
```
When utilizing both positional and keyword arguments the positional arguments must come before the keyword arguments. Providing a positional argument after a keyword argument will result in a `SyntaxError`
```python
>>> meals(breakfast='toast', 'sandwich', dinner='tacos')
  File "<stdin>", line 1
SyntaxError: positional argument follows keyword argument
```

<br>

### Default Arguments
Default values for parameters can be defined and the default value will be used if no corresponding argument was provided when calling the function. To set a default value follow the parameter of interest with an equals sign (`=`) and the default value.
```python
>>> def meals(breakfast, lunch, dinner='tacos'):
        '''Builds a dictionary from the positional arguments and returns the dictionary'''

        return {'breakfast': breakfast, 'lunch': lunch, 'dinner': dinner}

>>> meals('toast', 'sandwich')
{'breakfast': 'toast', 'lunch': 'sandwich', 'dinner': 'tacos'}
```
>**`Note #2:`**  When default arguments are defined they must follow non-default arguments   

```python
>>> def meals(breakfast='toast', lunch, dinner):
        '''Builds a dictionary from the positional arguments and returns the dictionary'''

        return {'breakfast': breakfast, 'lunch': lunch, 'dinner': dinner}
 
  File "<stdin>", line 1
SyntaxError: non-default argument follows default argument
```
>**`Note #3:`** Default parameter values are calculated when the function is defined, not when the function is ran. This can result in the common error of using a mutable data type (e.g. list) as a default parameter, remember python uses pass by assignment as was noted above.

To further demonstrate this, revisit the examples provided in the [Pass by Assignment](#pass-by-assignment) section. The expected behavior of `broken_function()` is that each time the function is called it runs with an empty list `var`, then appends the value of `arg` to this list, and prints the single-item list.

However, it does not work as intended. The list `var` is only empty the first time `broken_function()` is called.
```python
>>> def broken_function(arg, var=[]):
	var.append(arg)
	print(var)
	
>>> broken_function('first call')
['first call']
>>> broken_function('second call')
['first call', 'second call']
```


A solution to this problem is to set the default value of `var` to the immutable value `None` and setting the value of `var` to an empty list inside of the function.
```python
>>> def improved_function(arg, var=None):
	if var is None:
		var = []
	var.append(arg)
	print(var)
	
>>> improved_function('first call')
['first call']
>>> improved_function('second call')
['second call']
```
>**`Note #4:`** `None` is a special value in Python. `None` can be used as a placeholder when there is nothing and is not the same as the boolean value `False`. However when evaluated as a boolean it will appear false, but can be distinguished from `False` using the `is` operator.

```python
>>> var = None
>>> if var:
	print('var is nothing')
else:
	print('var is something')

var is something
>>> 
>>> # Distinguish None and False
>>> if var is None:
	print('var is nothing')
else:
	print('var is something')
	
var is nothing
```
>**`Note #4.1:`** Zero-value integer or floats, empty strings (`''`), empty lists (`[]`), empty tuples(`()`), dictionaries (`{}`), and sets (`set()`) are all `False` when evaluated as a boolean but are not the same as `None`

```python
>>> def what_is_it(it):
	if it is None:
		print(it, 'is None')
	elif it:
		print(it, 'is True')
	else:
		print(it, 'is False')
	
>>> what_is_it(None)
None is None
>>> what_is_it([])
[] is False
>>> what_is_it(1)
1 is True
```

<br>

---

<br>

## Flexible Arguments
Flexible arguments allow a variable number of arguments to be passed to a function.

### Positional Arguments `*`
`*` groups all the arguments passed to a function into a tuple of parameter values. It is common practice to use `*args` for this purpose but `args` does not have to be used. The name `args` is not what is important, what is important is that it is preceded with a single asterisk ( `*` ). If the function has a mix of *required* positional arguments and flexible arguments, list the required arguments first and `*args` goes at the end.
```python
>>> def add_all(*args):
	'''Sums all the values in *args and returns the sum'''

	sum_all = 0
	for number in args:
		sum_all += number
	return sum_all

>>> add_all(1,5)
6
>>> add_all(5, 10, 20)
35
```
<br>

### Keyword Arguments `**`
`**` groups all the keyword arguments passed to a function into a dictionary, where the keys are the argument names and their values are the corresponding values in the dictionary. It is common practice to use `**kwargs` (keyword arguments) for this purpose but is not required, again what is important is the name is preceded by double asterisks ( `**` ) not the name itself.
```python
>>> def print_all(**kwargs):
        '''Prints the key value pairs of **kwargs'''

	for key, value in kwargs.items():
		print(key + ' : ' + value)

>>> print_all(breakfast='toast', lunch='sandwich')
breakfast : toast
lunch : sandwich
>>> print_all(breakfast='toast', lunch='sandwich', dinner='tacos')
breakfast : toast
lunch : sandwich
dinner : tacos
```
>**`Note #5:`** When using `*args` and `**kwargs` it is important to remember it is not the names `args` and `kwargs` that are important, rather it is that the name are preceded by a single `*` or double `**` asterisk

>**`Note #6:`** When using a combination of argument types the order is (1) Required positional arguments, (2) Optional positional arguments (`*args`), and (3) Optional keyword arguments (`**kwargs`)

<br>

---

<br>

## Functions are Objects
Like strings, tuples, lists, dictionaries, and many other things in Python, functions are also objects.  Functions can be assigned to variables, and item in a list or dictionary, used as arguments to other functions, and be returned from functions.
```python
>>> def add_args(arg1, arg2):
        '''Print the value of arg1 +arg2'''

	print(arg1 + arg2)

	
>>> def multiply_args(arg1, arg2):
        '''Print the value of arg1 * arg2'''

	print(arg1 * arg2)

	
>>> def compute(func, arg1, arg2):
        '''Call the function passed in and pass arg1 and arg2 to that function'''

	func(arg1, arg2)

	
>>> # Call add_args passing in the values of 5 and 3
>>> add_args(5, 3)
8
>>> # Call compute passing in the function add_args and the values 5 and 3
>>> compute(add_args, 5, 3)
8
>>> # Call multiply_args passing in the values 2 and 5
>>> multiply_args(2, 5)
10
>>> # Call computer passing in the function multiply_args and the values 2 and 5
>>> compute(multiply_args, 2, 5)
10
>>> # Create a list of the functions add_args and multiply_args
>>> math_functions = [add_args, multiply_args]
>>> # Call the add_args function using the list math_functions and passing in the values 5 and 3
>>> math_functions[0](5, 3)
8
>>> # Call the multiply_args function using the list math_functions and passing in the values 2 and 5
>>> math_functions[1](2, 5)
10
```
This can also be combined with the use of `*args` and `**kwargs`
```python
>>> def sum_args(*args):
        '''Sum all the values in *args'''

        return sum(args)

>>> def use_positional_args(func, *args):
        '''Call the function passed in as func and pass *args to that function'''

        return func(*args)

>>> use_positional_args(sum_args, 1, 2, 3)
6
>>> use_positional_args(sum_args, 1, 2, 3, 4, 5)
15
```

<br>

---

<br>

## Anonymous `lambda` Functions
A lambda function is an anonymous function because it is not given a name when defined (`def name_of_function:`) as has been shown in all previous examples. These functions provide a quick, and potentially dirty, way to write function that may not be suitable for all uses but do have helpful implementations. Generally they can be useful to anonymously implement simple functionalities embedded within larger expressions. 

The syntax of a lambda function is the keyword `lambda` followed the name of the arguments, a colon `:` and then the function definition.

To compare use the prior add_arg functions:
```python
>>> def add_args(arg1, arg2):
        print(arg1 + arg2)

>>> add_args(5,3)
8
``` 
This basic normal function can be rewritten as a lambda function:
```python
>>> add_args_2 = lambda arg1, arg2: arg1 + arg2
>>> add_args_2(5,3)
8
```

<br>

---

<br>

## Generator Functions
**Generator:** a Python sequence creation object. 

A generator object can be used to iterate over large sequences when it is inefficient to store the entire source of data in memory. Generators differ from normal functions in that each time a generator is iterated over the generator tracks where it was each time it is called in contrast to normal function which have no memory of previous calls (i.e. a normal function starts at the first line of function and at the same state).

**Generator Function:** a function that when called produces a generator object.

Similar to normal functions a generator function is defined using `def`, however to return a value use the keyword `yield` rather than `return`.

```python
>>> def number_sequence(n):
        '''Generates values from 0 up to (not including) n'''
        i = 0
        while i < n:
            yield i
            i += 1

>>> number_sequence
<function number_sequence at 0x7f8c75903160>
>>> generator_sequence = number_sequence(3)
>>> generator_sequence
<generator object number_sequence at 0x7f8c759026d0>
>>> for num in generator_sequence:
         print(num)

0
1
2
```
>**`Note #7:`** A generator can only be ran a single time since the generator object is not stored in memory. A generator object creates the values only when called (i.e. on the fly) and does not remember its values (i.e. cannot restart or back up a generator). This differs from, for example, a list can be iterated over multiple times since its values are store in memory.

Trying to iterator over generator_sequence again there are no values to print. Contrasted with printing the elements of a list multiple times.
```python
# Iterator over generator_sequence again.
>>> for num in generator_sequence:
        print(num)

>>>
>>> # Iterator over list twice
>>> my_list = [1, 2, 3]
>>> for num in my_list:
        print(num)

1
2
3
>>> for num in my_list:
        print(num)

1
2
3
```

<br>

---

<br>

## Closures
**Closure:** is a tuple of variables that are no longer in scope but are required by a function to run

Below the function `outer_function()` defines the nested function `inner_function()` which prints the value of `x`, and `outer_function()` returns `inner_function()`
* `function = outer_function()` assigns the `inner_function()` to the variable `function`
* When `function()` is then called it prints the value of `x`, the closure makes `x` observable when `function()` (i.e. `inner_function()`) is called

```python
>>> def outer_function():
	x = 15
	def inner_function():
		print(x)
	return inner_function

>>> function = outer_function()
>>> function()
15
>>> type(function.__closure__)
<class 'tuple'>
>>> function.__closure__[0].cell_contents
15
```
```python
>>> x = 150
>>> def outer_function(number):
	def inner_function():
		print(number)
	return inner_function

>>> function = outer_function(x)
>>> function()
150
>>> # Delete the variable x
>>> del(x)
>>> print(x)
Traceback (most recent call last):
  File "<pyshell#185>", line 1, in <module>
    print(x)
NameError: name 'x' is not defined
>>> # The value of x persists in the function's closure
>>> function()
150
```
<br>

---

<br>

## Decorators
**Decorator:** a function that takes a function as an input and returns a modified version of the function with modified behavior. Decorators can become useful when an existing function needs to be modified/updated with out changing the source code for the function (e.g. adding debugging statements).
* Use `@` followed by the decorator name
* For the decorator to return a modified function it is generally helpful to define a new function that is returned (e.g. `wrapper()`)
* Decorators make use of nested functions, non-local variables, and closures.

First define a function that will be decorated `add()`
```python
>>> def add(x, y):
	return x + y
```
Define the decorator function (`double()`) and within it define the new function (`wrapper()`) that will be returned
* `wrapper()` takes the two arguments and passes them to the function that was passed to `double()`

```python
>>> def double(func):
	# Define the new function that will be modified
	def wrapper(x, y):
		# Call the passed in function with modified arguments
		return func(x*2, y*2)
	# Return the modified function
	return wrapper
```
Now `add()` can be passed to the function `double()` and assign the result to `new_addition()`
* `new_addition()` is equal to `wrapper()` which calls `add()` after doubling each argument

```python
>>> new_addition = double(add)
>>> new_addition(2, 4)
12
```
Using `@double` prior to the definition of add is equivalent to `add = double_args(add)`
```python
>>> @double
def add(x, y):
    return x + y

>>> add(2, 4)
12
```
More than one decorator can be used on a function
```python
>>> def document_a_function(func):
	def wrapper(*args, **kwargs):
	    print('Running:', func.__name__)
	    print('Positional args:', args)
	    print('Keyword args:', kwargs)
	    result = func(*args, **kwargs)
	    print('Result:', result)
	    return result
	    return wrapper

>>> @document_a_function
@double
def add (x, y):
	return x + y

>>> add(2, 4)
Running: wrapper
Positional args: (2, 4)
Keyword args: {}
Result: 12
12
```
### Function Metadata
One potential issue with the use of decorators is the decorator obscures the decorated function's metadata (e.g. the function docstring or name)

```python
>>> def sleep_n_seconds(n=10):
	'''Pause processing for n seconds

	Args:
		n (int): The number of seconds to pause for.
	'''
	time.sleep(n)

	
>>> print(sleep_n_seconds.__doc__)
Pause processing for n seconds

	Args:
		n (int): The number of seconds to pause for.
	
>>> print(sleep_n_seconds.__name__)
sleep_n_seconds
```
If `sleep_n_seconds()` is decorated, the decorator will obscure `sleep_n_seconds()`'s metadata. Below `sleep_n_seconds()` gets decorated with `timer()`.
* Printing the `__name__` attribute shows the name of the function is `wrapper`
  * This is because when defining the `timer` decorator the nested function `wrapper()` is returned
    * The decorator overwrites the `sleep_n_seconds()` function
    * Trying to then access `sleep_n_seconds()` docstring the code is now referencing the nested function `wrapper()` which is returned by the decorator

```python
>>> def timer(func):
	'''A decorator that prints how long a function takes to run.

	Args:
		func(callable): The function being decorated
	Returns:
		callable: The decorated function
	'''
	def wrapper(*args, **kwargs):
		# Get the current time
		t_start = time.time()
		# Call the decorated function and store the result
		result = func(*args, **kwargs)
		# Get the total time it took to run the function
		t_total = time.time() - t_start
		print('{} took {}s'.format(func.__name__, t_total))
	return wrapper

>>> @timer
def sleep_n_seconds(n):
        '''Pause processing for n seconds

	Args:
		n (int): The number of seconds to pause for.
	'''
	time.sleep(n)

	
>>> sleep_n_seconds(5)
sleep_n_seconds took 5.004786729812622s
>>>
>>> print(sleep_n_seconds.__name__)
wrapper
>>> print(sleep_n_seconds.__doc__)
None
```
This limitation can be overcome using the `wraps` function from the `functools` module
```python
>>> from functools import wraps
>>> def timer(func):
	'''A decorator that prints how long a function takes to run.

	Args:
		func(callable): The function being decorated
	Returns:
		callable: The decorated function
	'''
	@wraps(func)
	def wrapper(*args, **kwargs):
		# Get the current time
		t_start = time.time()
		# Call the decorated function and store the result
		result = func(*args, **kwargs)
		# Get the total time it took to run the function
		t_total = time.time() - t_start
		print('{} took {}s'.format(func.__name__, t_total))
	return wrapper

>>> @timer
def sleep_n_seconds(n):
	'''Pause processing for n seconds.

	Args:
		n (int): The number of seconds to pause for.
	'''
	time.sleep(n)

	
>>> print(sleep_n_seconds.__doc__)
Pause processing for n seconds.

	Args:
		n (int): The number of seconds to pause for.
```
<br>

### Decorators with Arguments
For a decorator to incorporate arguments the decorator function (`run_n_times`) must return a decorator, rather than a function that is a decorator
* `decorator(func)`: the new decorator defined inside the function 
  * This function has a nested `wrapper()` function like all other decorator examples
* Since `wrapper()` is in the `run_n_times()` scope the function has access to `n` to run the function `n` amount of times
* Then `wrapper()` is returned
* `run_n_times()` then returns the `decorator()` function which can then be used as a decorator
* `@run_n_times(n)` indicates that `run_n_times()` is being called and is decorating `print_sum()`
  * Since `run_n_times()` returns a decorator function it can be used to decorate `print_sum()`

```python
>>> def run_n_times(n):
	'''Define and return a decorator'''
	def decorator(func):
		def wrapper(*args, **kwargs):
			for i in range(n):
				func(*args, **kwargs)
		return wrapper
	return decorator

>>> @run_n_times(3)
def print_sum(x, y):
	print(x + y)

	
>>> print_sum(2, 3)
5
5
5
```

<br>

---

<br>

## Exceptions
**Exceptions:** are specific code that is executed when a corresponding error occurs during code execution.

An example would include attempting to access a out-of-range index of a list.
```python
>>> my_list = [2, 5, 7]
>>> my_list[5]
Traceback (most recent call last):
  File "<pyshell#368>", line 1, in <module>
    my_list[5]
IndexError: list index out of range
```
It is best practice to include exception handling throughout code anywhere an exception may occur. An exception handling can provide helpful information about the error that occurred and stop the execution of the code.

Error handling can be implemented by wrapping code with `try`, and using `except` for the error handling. 

When using `try-except`:
1. First Python runs the code contained within the `try` block (`return x ** 0.5`)
2. If, and only if, the code cannot execute successfully due to an error an exception is raised and the code within the `except` block (`print('x must be an int or float')`) is executed

```python
>>> def sqrt(x):
        '''Returns the square root of a number (x)'''
        try:
            return x ** 0.5
        except:
            print('x must be an int or float')

>>> sqrt(4)
2.0
>>> sqrt('four')
x must be an int or float
```
When `except` is used, without any additional arguments, it will catch any type of exception. However, to provide more detailed information on the error that occurred it can be helpful to include an exception handler for each type of exception. There is not a limit in the number of specific exception handlers that can be used.
```python
>>> my_list = [4, 'four']
>>> def sqrt(x):
        '''Returns the square root of a number(x)'''
        try: 
            return x ** 0.5
        except TypeError as type_err:
            print('Type Error:', type_err)
        except IndexError as index_err:
            print('Index Error:', index_err)
        except Exception as other:
            print('Other Error:', other)
>>>
>>> sqrt(my_list[0])
2.0
>>> sqrt(my_list[1])
Type Error: unsupported operand type(s) for ** or pow(): 'str' and 'float'
>>>
>>> sqrt(my_list[2])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```
The keyword `raise` can be used to throw and exception if a specific condition occurs that should not be executed. 
```python
>>> def sqrt(x):
        '''Returns the square root of a number (x)'''
        if x < 0:
            raise ValueError ('x should be a non-negative integer. The value of x was: {}'.format(x))
        try:
            return x ** 0.5
        except TypeError as type_err:
             print('Type Error:', type_err)
        except IndexError as index_err:
            print('Index Error:', index_err)
        except Exception as other:
            print('Other Error:', other)

>>> sqrt(-4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 4, in sqrt
ValueError: x should be a non-negative integer. The value of x was: -4
```