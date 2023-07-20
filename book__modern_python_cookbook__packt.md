# Modern Python Cookbook (Packt Publishing)

## Chapter 1. Numbers, Strings, and Tuples

### decimal (for currencies)

```text
>>> from decimal import Decimal
>>> tax_rate = Decimal('7.25')/Decimal(100)
>>> purchase_amount = Decimal('2.95')
>>> tax_rate * purchase_amount
Decimal('0.213875')
```

### binary

```text
>>> composite_byte = 0b01101100
>>> bottom_6_mask =  0b00111111
>>> bin(composite_byte >> 6)
'0b1'
>>> bin(composite_byte & bottom_6_mask)
0b101100'
```

### fractions

```text
>>> from fractions import Fraction
>>> sugar_cups = Fraction('2.5')
>>> scale_factor = Fraction(5/8)
>>> sugar_cups * scale_factor
Fraction(25, 16)

In [7]: from fractions import Fraction
In [8]: Fraction(152/23)
Out[8]: Fraction(7440729819133863, 1125899906842624)
In [9]: Fraction(152,23)
Out[9]: Fraction(152, 23)

>>> Fraction(24,16)
Fraction(3, 2)
```

### floats, rouding

```text
>>> answer= (19/155)*(155/19)
>>> round(answer, 3)
1.0
```

### the math module

```text
>>> (19/155)*(155/19) == 1.0
False
>>> math.isclose((19/155)*(155/19), 1)
True
```

- `_math.fsum()_` better than `_sum()_`
- explore other `_math.*_` functions
- complex numbers: `_cmath_` module

### integer division

```text
>>> total_seconds = 7385
>>> hours = total_seconds//3600
>>> remaining_seconds = total_seconds % 3600
```

or

```text
>>> total_seconds = 7385
>>> hours, remaining_seconds = divmod(total_seconds, 3600)
```

note: using new division operators like // need `>>> from __future__ import
division` in python 2

### string operations

```text
>>> colon_position = title.index(':')
>>> discard_text, post_colon_text = title[:colon_position], title[colon_position+1:]
>>> pre_colon_text, _, post_colon_text = title.partition(':')
>>> post_colon_text = post_colon_text.replace(' ', '_')
>>> post_colon_text = post_colon_text.replace(',', '_')
>>> from string import whitespace, punctuation
>>> for character in whitespace + punctuation:
...     post_colon_text = post_colon_text.replace(character, '_')
>>> post_colon_text = post_colon_text.lower()
>>> post_colon_text = post_colon_text.strip('_') # on both ends
>>> while '__' in post_colon_text:
...    post_colon_text = post_colon_text.replace('__', '_')
```

We made use of slice notation to decompose a string. A slice has two parts:
[start:end]. A slice always includes the starting index. String indices always
start with zero as the first item. It never includes the ending index.

parsing numbers:

```text
>>> '1298'.isnumeric()
True
```

regex parsing

```text
>>> import re
>>> pattern_text = r'(?P<ingredient>\w+):\s+(?P<amount>\d+)\s+(?P<unit>\w+)'
>>> pattern = re.compile(pattern_text)
>>> match = pattern.match(ingredient)
>>> match is None
False
>>> match.groups()
('Kumquat', '2', 'cups')
>>> match.group('ingredient')
'Kumquat'
```

regexes can span several lines

```text
>>> ingredient_pattern = re.compile(
... r'(?P<ingredient>\w+):\s+' # name of the ingredient up to the ":"
... r'(?P<amount>\d+)\s+'      # amount, all digits up to a space
... r'(?P<unit>\w+)'           # units, alphanumeric characters
... )
```

format strings

```text
>>> '{id:3s}  : {location:19s} :  {max_temp:3d} / {min_temp:3d} /
    {precipitation:5.2f}'.format(
...   id=id, location=location, max_temp=max_temp,
        min_temp=min_temp, precipitation=precipitation )
'IAD  : Dulles Intl Airport :   32 /  13 /  0.40'

>>> '{id:3s}  : {location:19s} :  {max_temp:3d} / {min_temp:3d} /
{precipitation:5.2f}'.format_map( data )
'IAD  : Dulles Intl Airport :   32 /  13 /  0.40'

>>> '{id:3s}  : {location:19s} :  {max_temp:3d} / {min_temp:3d} /
{precipitation:5.2f}'.format_map( vars() )
'IAD  : Dulles Intl Airport :   32 /  13 /  0.40'
```

process string as a list

```text
>>> title_list = list(title)
>>> colon_position = title_list.index(':')
>>> ...
>>> title = ''.join(title_list)
```

unicode characters

```text
\uxxxx     for 4 hex digits
\Uxxxxxxxx for 8 hex digits
\N{UNICODE_NAME}

# display raw strings
>>> r"\w+"
'\\w+'
```

encoding strings

- to specify an ecnoding for I/O globally: `export PYTHONIOENCODING=UTF-8`
- to specify an ecnoding per FH:

```text
with open('some_file.txt', 'w', encoding='utf-8') as output:
    print( 'You drew \U0001F000', file=output )
```

- when opening a file in byte mode:

```text
>>> string_bytes = 'You drew \U0001F000'.encode('utf-8')
>>> string_bytes
b'You drew \xf0\x9f\x80\x80'
```

- when retrieving a stream of bytes with urllib for instance:

```text
>>> document = forecast_text.decode("UTF-8")
>>> document[:80]
'<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.or">'
```

The b prefix is gone. We've created a proper string of Unicode characters from the stream of bytes.

NOTE: for web scraping, use <https://www.crummy.com/software/BeautifulSoup/>

### using tuples

the result of a regex match groups() method is a tuple

create a tuple: `foo = ("bar", "baz", 2.0)`

singleton tuple: `'355,' -> returns (355,), not 355)`

```text
>>> ingredient, amount, unit = my_data # extract 3 items
>>> foo, _, bar = my_data # discard 2nd item (convention)

>>> len(t)
3
>>> t.count('2')
1
>>> t.index('cups')
2
>>> t[2]
'cups'
>>> t.index('Rice')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: tuple.index(x): x not in tuple
>>> 'Rice' in t
False
```

## Chapter 2. Statements and Syntax

### Long lines

We can use \ at the end of a line to continue onto the next line.

We can leverage Python's rule that a statement can span multiple logical lines because the (), the [], and the {} characters must balance.

In addition to using () and \, we can also exploit the way Python automatically concatenates adjacent string literals to make a single, longer literal; ("a" "b") is the same as ab.

for long imports:

```text
>>> from math import (sin, cos, tan,
...    sqrt, log, frexp)
```

### Docstrings

use ReStructuredText (RST) markup (see details in the book, 2 paragraphs)

RST spec: <http://docutils.sourceforge.net/>

```python
#!/usr/bin/env python3
'''
My First Script: Calculate an important value.
'''
```

type inference (not built-in, just advisory): `color = 355/113 # type: float`

### Complex if statements

use assert to verify a complex condition, throws AssertionError if false

use while loop and assertion:

```python
while password_text != confirming_password_text:
    password_text= getpass()
    confirming_password_text= getpass("Confirm: ")
assert password_text == confirming_password_text
```

use for loop + break + cover edge cases

```python
for position in range(len(sample_2)):
    if sample_2[position] in '=:':
        name, value = sample_2[:position], sample_2[position+1:]
        break
else: # applies to the for loop, fires if break fires
    if len(sample_2) > 0:
        name, value = sample_2, None
    else:
        name, value = None, None
```

### Exceptions

- Use except Exception: as the most general kind of exception managing

- Don't capture BaseException, SystemError, RuntimeError or MemoryError

- Don't capture SystemExit, KeyboardInterrupt or GeneratorExit

- Don't use `except:` with no class, as it will capture the above

- chained exceptions to make a generic one out of several types

```python
except (IndexError, NameError) as exception:
  raise Error("index problem") from exception
```

`raise` alone re-throws the caught exception
`raise ExceptionType(...)` raises a different exception
`raise ExceptionType(...) from exception` raises a chained exception

### Managing context with `with`

`with` creates a context manager:

```python
target_path = pathlib.Path('code/test.csv')
  with target_path.open('w', newline='') as target_file:
    do something with target_file
    ...
  target_file is realased here
```

when exiting the block (normally or through an exception), the context manager
releases the resource (file, db connection, lock (see threading + locks in python3), etc)

define our own context managers: see the `contextlib` module

## Chapter 3: Function definitions

### optional parameters

a default value in the function definition is what makes a parameter optional

```python
# call dice() for Craps. We'll need to use dice(6) for yams
def dice(n=2):
    return tuple(die() for x in range(n)) # with generator added
```

positional values must be provided first because the order matters. For
example, dice(2, sides=8). When using all named arguments, order doesn't
matter.

### super flexible KW parameters

```python
def rtd2(distance, rate, time, **keywords):
  print(keywords)
```

```python
def rtd2(**keywords):
        rate= keywords.get('rate', None)
        time= keywords.get('time', None)
        distance= keywords.get('distance', None)
```

### forcing KW only arguments

The * character has two meanings in the definition of a function:

- It's used as a prefix for a special parameter that receives all the unmatched
  positional arguments. We often use *args to collect all of the positional
  arguments into a single parameter named args.
- It's used by itself, as a separator between parameters that may be applied
  positionally and parameters which must be provided by keyword.

```python
# doesn't accept positional arguments
def wind_chill(*, start_T, stop_T, step_T, start_V, stop_V, step_V, path):
```

### explicit typing

Create a type class

```python
from decimal import Decimal
from typing import *
Number = Union[int, float, complex, Decimal]
```

annotate the parameters and function return to specify types (Dict comes from typing, not like dict())

```python
def temperature(*,
    f_temp: Optional[Number]=None,
    c_temp: Optional[Number]=None) -> Dict[str, Number]:
```

NOTE: type hints have no influence at compile or runtime, but are used by mypy
(<http://mypy-lang.org>) when run on the source code to detect possible issues

how to write type hints for complex data structures? for instance:

```python
a = {
  (1, 2, 3): ['Poe', 'E'],
  (3, 4, 5): ['Near', 'a', 'Raven'],
}
# type hint is: Dict[Tuple[int, int, int], List[str]]
```

### partial functions

We can follow the Forcing keyword-only arguments with the * separator recipe. We might change the basic haversine function to look like this:

```python
def haversine(lat_1: float, lon_1: float,
    lat_2: float, lon_2: float, *, R: float) -> float:
```

Create a partial function using the keyword parameter:

```python
from functools import partial
nm_haversine = partial(haversine, R=NM)
```

The partial() function builds a new function from an existing function and a concrete set of argument values. The nm_haversine() function has a specific value for R provided when the partial was built.

**or** it can also be done using the 1st argument instead of the last:

```python
def haversine(R: float, lat_1: float, lon_1: float,
    lat_2: float, lon_2: float) -> float:
```

Create a partial function using the positional parameter (NM => R at build time):

```python
from functools import partial
nm_haversine = partial(haversine, NM)
```

here's a third way to wrap a function—we can also build a lambda object. This will also work:

```python
nm_haversine = lambda *args: haversine(*args, R=NM)
```

### Writing clear documentation strings with RST markup

```python
def Twc(T: float, V: float) -> float:
    """Computes the wind chill temperature

    The wind-chill, :math:`T_{wc}`, is based on
    air temperature, T, and wind speed, V.

    See https://en.wikipedia.org/wiki/Wind_chill

    math::
    T_{wc}(T_a, V) = 13.12 + 0.6215 T_a - 11.37 V^{0.16} + 0.3965 T_a V^{0.16}

    :param T: Temperature in °C
    :param V: Wind Speed in kph
    :returns: Wind-Chill temperature in °C
    :raises ValueError: for wind speeds under over 4.8 kph or T above 10°C
    """
```

```python
def wind_chill_table():
        """Uses :func:`Twc` to produce a wind-chill
        table for temperatures from -30°C to 10°C and
        wind speeds from 5kph to 50kph.
        """
```

### Do not use tail recursion

python has a limited stack, so use recursion sparsely. For tail recursion, prefer using reduction (accumulators). don't do:

```python
def fact(n: int) -> int:
    if n == 0:
        return 1
    return n*fact(n-1)
```

but rather:

```python
def prod(int_iter):
    p = 1
    for x in int_iter:
        p *= x
    return p

def fact(n):
        return prod(range(1, n+1))
```

for more complex cases, use memoization

```python
from functools import lru_cache

@lru_cache(128)
def fibo(n):
    if n <= 1:
        return 1
    else:
        return fibo(n-1)+fibo(n-2)
```

fibonacci with generators:

```python
def fibo_iter():
    a = 1
    b = 1
    yield a
    while True:
        yield b
        a, b = b, a+b
```

### Writing reusable scripts with the script library switch

Move actions in `def ... :` statements, then, if it should run as a standalone script _and_ a library, add this at the end:

```python
if __name__ == "__main__":
    my_function()
```

> "The most important rule for Python is that an import of a module is essentially the same as running the module as a script[...]
>
> When Python runs a script, it sets a number of built-in special variables. One of these is `__name__` . This variable has two different values, depending on the context in which the file is being executed:
>
> - The top-level script, executed from the command line: In this case, the value of the built-in special name of `__name__` is set to `__main__`
> - A file being executed because of an import statement: In this case, the value of `__name__` is the name of the module being created.
>
> This kind of script can be viewed as a spike solution . Our spike solution should evolve towards a more refined solution as soon as we're sure that it works."

## Chapter 4: built-in data structures

Create a set: `foo = {"bar", "baz"}`

Create a list: `foo = ["bar", "baz"]`

Create a dict: `foo = {"bar": 1, "baz": 2}`

Numbers, strings and tuples are immutable
Since a list, dict, or set object is mutable, they can't be used as items in a set. It's impossible to build a set of list items, for example.
Similarly, dictionary keys must be immutable. We can use a number, or a string, or a tuple as a dictionary key. We can't use a list, or a set, or another mutable mapping as a dictionary key.

the collections.abc module provides a kind of road map through the built-in collections. The collections.abc module defines the Abstract Base Classes (ABCs) that support the concrete classes we use.

- Set: The unique feature is that items are either members or not. This means duplicates can't be handled:
  - Mutable set: The set collection
  - Immutable set: The frozenset collection
- Sequence: The unique feature is that items are provided with an index position:
  - Mutable sequence: The list collection
  - Immutable sequence: The tuple collection
- Mapping: The unique feature is that each item has a key that refers to a value:
  - Mutable mapping: The dict collection
  - Immutable mapping: Interestingly, there's no built-in frozen mapping

The collections module contains a number of variations on the built-in collections. These include:

- namedtuple: A tuple that offers names for each item in a tuple. It's slightly more clear to use rgb_color.red than rgb_color[0].
- deque: A double-ended queue. It's a mutable sequence with optimizations for pushing and popping from each end.
- defaultdict: A dict that can provide a default value for a missing key.
- Counter: A dict which is designed to count occurrences of a key. This is sometimes called a multiset or a bag.
- OrderedDict: A dict which retains the order in which keys were created.
- ChainMap: A dict which combines several dictionaries into a single mapping.
- heapq module which defines a priority queue implementation.
- bisect module includes methods for searching a sorted list very quickly

### Building lists

`foo.append(...)` mutates the list in place and returns nothing

List comprehension: `foo = [ bar.baz() for bar in <whatever returns a list> <if optional clause> ]`

can also use the list() function on the generator expression: `foo = list(bar.baz() for bar in <whatever returns a list>)`

Create list with initial size: `some_list = [None for i in range(100)]`

Can use functions on a list, like `min()`, `max()`, `mean()`
Find index of lowest value: `foo.index(min(foo))`

Extend a list: `foo = list1 + list2` or `list1.extend(list2)`
Insert in a list: `foo.insert(0, "value")` inserts in 1st postion.

### Slicing and dicing a list

slice a list in 2 lists, at row 4 (index is 3, so the n: notation specifies the index to cut before, whereas :n specifies the first index to include): `head, tail = log_rows[:4], log_rows[4:]`

full notation for slices is `start:stop:step`, any parameter can be omitted, defaults are `0:last index+1:1`

- `[0::3]` slice starts with the first row, and includes every third row.
- `[1::3]` slice starts with the second row, and includes every third row.

`zip(list1, list2)` interleaves 2 lists and makes a list of tuples.

```python
# flatten the tuples
paired_rows = list( zip(tail[0::3], tail[1::3]) )
[a+b for a,b in paired_rows]
```

The slicing technique works for lists, tuples, strings, and any other kind of sequence.

### Deleting from a list

We can remove elements from a list because it is mutable, unlike a tuple for instance.

delete first 4 elements from a list: `del(list1[:4])`

remove a matching element from a list: `list1.remove("whatever")` (used for instance with an empty string to filter out empty fields in a list)

remove an element by index: `list1.pop(index)` (mutates the object and returns the removed element)

filter with a function or a lambda: `filtered = list(filter(filter_function, list1))` (the output of the filter function is an iterable, hence the need to call `list()` on it)

### Reversing a list

use the `reverse()` method, which will mutate it in place

OR use a trick: `reversed_list = list1[::-1]` (slice with negative step value)

Careful, a list is addressed by reference, so to make a copy of it, use `list1.copy()`; this is why we use `fields_copy2 = fields[::-1]` which is a shallow copy, saving 1 instruction

### Using set methods and operators

We can build a set using the `set()` function to convert an existing collection to a set, and we can add to it using the `add()` method, and use the `update()` method, the union operator `|` or the set's `union` method.

```python
collection.add(item)             # mutates, single item
colection.update({item, ...})     # mutates, multiple items
collection.union({item})         # returns a set, doesn't mutate
collection | {item, ...}          # returns a set
```

other operators:

- `|`  for union, often typeset as A ∪ B
- `&`  for intersection, often typeset as A ∩ B
- `^`  for symmetric difference, often typeset as A Δ B
- `-`  for subtraction, often typeset as A - B

### Removing items from a set – remove(), pop(), and difference

```python
to_be_ignored = {'IP: 0.0.0.0', 'IP: 1.2.3.4'}
matches = {'IP: 111.222.111.222', 'IP: 1.2.3.4'}
matches - to_be_ignored             # {'IP: 111.222.111.222'}
matches.difference(to_be_ignored)     # {'IP: 111.222.111.222'}
```

```python
for item in to_be_ignored:
    if item in valid_matches: # could be replaced by try/catch on KeyError
        valid_matches.remove(item)
```

`pop()` on a set will remove one element at random. Throws KeyError on empty set

### Creating dictionaries – inserting and updating

It's essential that dictionary key objects be immutable. We cannot use a list, set, or dict as the key in a dictionary mapping. We can, however, transform a list into an immutable tuple, or make a set into a frozenset so that we can use one of these more complex objects as a key.

Specialized implementations of dictionaries in the collections module:

- defaultdict (no need to use `dict1.setdefault(key, <defaultvalue>)` if the key does not exist yet)
- OrderedDict
- Counter

```python
from collections import defaultdict
# passsing the int function object will initialize at 0
histogram = defaultdict(int)
for item in source:
    histogram[item] += 1
```

`Counter(<source iterable, list or other>)`will create a dictionary with items as key, and number or occurences as value, and will display data by descending number of occurences

`OrderedDict` will display data with consistent ordering (by order of insertion)

to display by key order: `for key in sorted(histogram): print(key, histogram[key])`

- We have the in-memory **dictionary**, **dict**, and the variations on this theme in the **collections** module. The collection only exists while our program is running.
- We also have persistent storage in the **shelve** and **dbm** modules. The data collection is a persistent file in the file system.

### Removing from dictionaries – the pop() method and the del statement

```python
amount = working_bets.pop('come odds') # returns the removed value, throws KeyError
del working_bets['come odds'] # returns nothing
```

`pop()` can be given a default value. If the key is not present, it will not raise an exception, but will return the default value instead.

### Controlling the order of dict keys

- Create an `OrderedDict`: This keeps keys in the order they are created
- Use `sorted()` on the keys: This puts the keys into a sorted order

### Making shallow and deep copies of objects

lists, dictionaries and sets are mutable, the rest isn't

python uses reference counting garbage collection

shallow copy of mapings and sets are done with the `copy()` method. Beware, if the values of the object are references (lists, mappings or sets), then the copy and the copied object will share the same references. It can be a problem when mutating the values inside the original or copied object, which will reflect the other's changed state.

Checking that 2 references are the same can be done using the `==` or `is` operators or the `id` function

```python
>>> some_list = [[2, 3, 5], [7, 11, 13]]
>>> another_list = some_list.copy()
>>> some_list is another_list
False
>>> some_list[0] is another_list[0]
True
```

to make deep copies instead of shallow ones, use `deepcopy` from the `copy` module.

### Avoiding mutable default values for function parameters

`def gather_stats(n, samples=1000, summary=Counter()):`

this is a really bad idea, because on the next iteration with no summary passed, it will reuse the summary variable which is already initialized in the scope. better do this:

```python
def gather_stats(n, samples=1000, summary=None):
      if summary is None: summary = Counter()
    ...
```

_Don't use mutable defaults for functions. A mutable object (set, list, dict) should not be a default value for a function parameter._

```python
def gather_stats(n, samples=1000, summary_func=lambda x:Counter(x)):
    summary = summary_func(
      sum(randint(1,6) for d in range(n)) for _ in range(samples))
    return summary

gather_stats(2, 12, summary_func=list)  # returns a list
gather_stats(2, 12)                              # returns a Counter
```

## Chapter 5: User Inputs and Outputs

### Using features of the print() function

- make use of the `sep=` and `end=` arguments of print() to control displaying of lists and lines

- make use of the `file=` argument to redirect output (`sys.stdout`, `sys.stderr`, etc)

- use context managers

  ```python
  from pathlib import Path
      target_path = Path("somefile.dat")
      with target_path.open('w', encoding='utf-8') as target_file:
          print("Some output", file=target_file)
          print("Ordinary log")
  ```

### Using input() and getpass() for user input

- input(): This prompts and reads simply

- getpass.getpass(): This prompts and reads passwords without an echo

- the readline module, if installed, improves the line editing capability

- imput string parsing with strptime:

  ```python
  raw_date_str = input("date [yyyy-mm-dd]: ")
  input_date = datetime.strptime(raw_date_str, '%Y-%m-%d').date()
  ```

    (will trigger a ValueError exception if incorrect input)

### Debugging with "format".format_map(vars())

- The vars() function builds a dictionary structure from a variety of sources.

- If no arguments are given, then by default, the vars() function will expand all the local variables. This creates a mapping that can be used with the format_map() method of a template string.

- Using a mapping allows us to inject variables using the variable's name into the format template. It looks as follows:

  ```python
  print(
      "mean={mean_size:.2f}, std={std_size:.2f}"
      .format_map(vars())
  )
  ```

- The `format_map()` method expects a single argument, which is a mapping. The format string uses `{name}` to refer to keys in the mapping. We can use `{name:format}` to provide a format specification. We can also use `{name!conversion}` to provide a conversion function using the `repr()`, `str()`, or `ascii()` functions.

- An alternative is to use `format(**vars())`. This alternative can give us some additional flexibility. For example, we can use this more flexible format to include additional calculations that aren't simply local variables:

  ```python
  print(
        "mean={mean_size:.2f}, std={std_size:.2f},"
        " limit2={sig2:.2f}"
        .format(sig2=mean_size+2*std_size, **vars())
       )
  # mean=1414.77, std=901.10, limit2=3216.97
  ```

### Using argparse to get command-line input

```python
parser = argparse.ArgumentParser()
parser.add_argument('-r', action='store',
               choices=('NM', 'MI', 'KM'), default='NM')  # optional argument, prefix - or --
parser.add_argument('p1', action='store', type=point_type) # 1st positional argument (required)
parser.add_argument('p2', action='store', type=point_type) # 2nd positional argument (required)
options = parser.parse_args()
```

- argparse typically raises `argparse.ArgumentTypeError`

- Using shell globbing on the command line (list of files in a directory), then: `parser.add_argument('file', nargs='*')`
  All of the names on the command line that do not start with the `-` character will be collected into the `file` value in the object built by the parser.

- The `-o` or `--option` arguments are often used to enable or disable features of a program. These are often implemented with `add_argument()` parameters of `action='store_true', default=False`.

- **Simple options with non-trivial objects**: The user sees this is as simple `-o` or `--option` arguments. We may want to implement this using a more complex object that's not a simple Boolean constant. We can use `action='store_const', const=some_object, default=another_object`.

- **Options with values**: We showed `-r unit` as an argument that accepted the string name for the units to use. We implemented this with an `action='store'` assignment to store the supplied string value. We can also use the `type=function` option to provide a function that validates or converts the input into a useful form.

- **Options that increment a counter**: One common technique is to have a debugging log that has multiple levels of detail. We can use `action='count', default=0` to count the number of times a given argument is present. The user can provide `-v` for verbose output and `-vv` for very verbose output.

- **Options that accumulate a list**: We might have an option for which the user might want to provide more than one value. We could, for example, use a list of distance values. We could have an argument definition with `action='append', default=[]`. This would allow the user to say `-r NM -r KM`

- **Show the help text**: If we do nothing, then `-h` and `--help` will display a help message and exit.

- **Show the version number**: It's common to have `--Version` as an argument to display the version number and exit. We implement this with `add_argument("--Version", action="version", version="v 3.14")`. We provide an action of `version` and an additional keyword argument that sets the version to display.

### Using cmd for creating command-line applications

- The core feature of the `cmd.Cmd` application is a **read-evaluate-print loop** (**REPL**). This kind of application works well when there are a large number of individual state changes and a large number of commands to make those state changes.

  ```python
  import cmd
  class Roulette(cmd.Cmd):
      def preloop(self):
              self.bets = {}
              self.stake = 100
              self.wheel = wheel()
      def do_bet(self, arg_string): # called on 'bet <whatever>'
        # do something
  ```

- If there's no `do_foo()` method, the command processor writes an error message. This is done automatically, we don't need to write any code at all.

- We can define `help_*()` methods that become part of the miscellaneous help topics.

- When any of the `do_*` methods return a value, the loop will end. We might want to add a `do_quit()` method that has `return True` as it's body. This will end the command-processing loop.

- We might provide a method named `emptyline()` to respond to blank lines. One choice is to do nothing quietly. Another common choice is to have a default action that's taken when the user doesn't enter a command.

- The `default()` method is evaluated when the user's input does not match any of the `do_*` methods. This might be used for more advanced parsing of the input.

- The `postloop()` method can be used to do some processing just after the loop finishes. This would be a good place to write a summary. This also requires a `do_*` method that returns a value—any non-`False` value—to end the command loop.

- The `prompt` attribute is the prompt string to write. For our example, we can do the following:

  ```text
  class Roulette(cmd.Cmd):
              prompt="Roulette> "
  ```

- The `intro` attribute is the introductory message.

- We can tailor the help output by setting `doc_header`, `undoc_header`, `misc_header`, and `ruler` attributes. These will all alter how the help output looks.

### Using the OS environment settings

```python
import os
def get_options(argv=sys.argv):
    default_units = os.environ.get('UNITS', 'KM')
    if default_units not in ('KM', 'NM', 'MI'):
        sys.exit("Invalid value for UNITS, not KM, NM, or MI")
    default_home_port = os.environ.get('HOME_PORT')
    parser = argparse.ArgumentParser()
    parser.add_argument('-r', action='store',
        choices=('NM', 'MI', 'KM'), default=default_units)
    parser.add_argument('p1', action='store', type=point_type)
    parser.add_argument('p2', nargs='?', action='store', type=point_type,
        default=default_home_port)
    options = parser.parse_args(argv[1:])
    if options.p2 is None:
        sys.exit("Neither HOME_PORT nor p2 argument provided.")
    return options
```

## Chapter 6: Basics of Classes and Objects

### Using a class to encapsulate data and processing

- **Single Responsibility Principle**: A class should have one clearly defined responsibility.

- **Open/Closed Principle**: A class should be open to extension-generally via inheritance, but closed to modification. We should design our classes so that we don't need to tweak the code to add or change features.

- **Liskov Substitution Principle**: We need to design inheritance so that a subclass can be used in place of the superclass.

- **Interface Segregation Principle**: When writing a problem statement, we want to be sure that collaborating classes have as few dependencies as possible. In many cases, this principle will lead us to decompose large problems into many small class definitions.

- **Dependency Inversion Principle**: It's less than ideal for a class to depend directly on other classes. It's better if a class depends on an abstraction, and a concrete implementation class is substituted for the abstract class.

### Designing classes with lots of processing

Most of the time, an object will contain all of the data that defines its internal state. However, this isn't always true. There are cases where a class doesn't really need to hold the data, but instead can hold the processing.

Some prime examples of this design are statistical processing algorithms, which are often outside the data being analyzed. The data might be in a `list` or `Counter` object. The processing might be a separate class.

```python
class CounterStatistics:
    def __init__(self, raw_counter:Counter):
        self.raw_counter = raw_counter
        self.mean = self.compute_mean()
        self.stddev = self.compute_stddev()
    def compute_mean(self):
        # process...
    def compute_stdded(self):
        # process
```

### Designing classes with little unique processing

#### Stateless objects (no setters, only getters)

```python
>>> from collections import namedtuple
>>> Card = namedtuple('Card', ('rank', 'suit'))
>>> eight_hearts = Card(rank=8, suit='\N{White Heart Suit}')
>>> eight_hearts
Card(rank=8, suit='♡')
>>> eight_hearts.rank
8
>>> eight_hearts.suit
'♡'
>>> eight_hearts[0]
8
```

#### Stateful objects with a new class (dynamic attributes)

```python
class Player:
  pass
p = Player()
p.stake = 100
```

#### Stateful objects using an existing class (same as above)

```python
from argparse import Namespace
from types import SimpleNamespace # either one of these two
Player = SimpleNamespace
p = Player()
p.stake = 100
```

### Optimizing small objects with \_\_slots__

```python
class Hand:
    __slots__ = ('hand', 'bet')
    def __init__(self, bet, hand=None):
        self.hand= hand or []
        self.bet= bet
    def __repr__(self):
        return "{class_}({bet}, {hand})".format(
            class_= self.__class__.__name__,
            **vars(self)
        )
```

Specifying \_\_slots__prevents addition of new attributes, highly optimized; it avoids the \_\_dict__ behaviour, which is dynamic.

See <https://docs.python.org/3/reference/datamodel.html#metaclass-example>

### Using more sophisticated collections

- `collections` module

  - `deque`: double-ended queue, optimized for append and pop on both ends
  - `defaultdict`: provide a default value for a missing key, like `lookup = defaultdict(lambda:"N/A")`
  - `Counter`: designed to count occurrences of a key, equivalen to `defaultdict(int)`
  - `OrderedDict`: dictionary with ordered keys
  - `ChainMap`: search through a list of dicts, in order like `config = ChainMap(dict1, dict2, dict3...)`

- `heapq` module includes a priority queue implementation

- `bisect` module includes methods for searching a sorted list

### Extending a collection – a list that does statistics

```python
class StatsList(list):
    def sum(self):
        return sum(v for v in self)
    def mean(self):
        return self.sum() / self.count()
```

There are abstract superclasses for all of the built-in collections. Rather than start from a concrete class, we can also start our design from an abstract base class:

```python
from collections.abc import Mapping
    class MyFancyMapping(Mapping):
        # ...
      # implement abstract methods
      def __getitem__():
      def __setitem__():
      def __delitem__():
      def __iter__():
      def __len__():
```

### Using properties for lazy attributes

```python
class LazyCounterStatistics:
  def __init__(self, raw_counter:Counter):
    self.raw_counter = raw_counter
  @property
  def sum(self):
    return sum(f*v for v, f in self.raw_counter.items())
  @property
  def count(self):
    return sum(f for v, f in self.raw_counter.items())
```

We used the `@property` decorator. This makes a method function appear to be an attribute. This can only work for method functions that have no argument values.

### Using settable properties to update eager attributes

```python
class Leg:
    def __init__(self):
        self._rate= rate
        self._time= time
        self._distance= distance
        # to know when the values should be recalculated
        self._changes= deque(maxlen=2)

    @property
    def rate(self):
        return self._rate

    @rate.setter
    def rate(self, value):
        self._rate = value
        self._calculate('rate')
    # repeat for other attributes

    # calc_* will be called by getattr() below automatically when
    # _calculate is called, which happens on a setter call
    def calc_distance(self):
        self._distance = self._time * self._rate

    def calc_time(self):
        self._time = self._distance / self._rate

    def calc_rate(self):
        self._rate = self._distance / self._time

    def _calculate(self):
        # look at the deque, recalculate all properties if
        # 2 or more are changed
        if change not in self._changes:
            self._changes.append(change)
        compute = {'rate', 'time', 'distance'} - set(self._changes)
        if len(compute) == 1:
            name = compute.pop()
            method = getattr(self, 'calc_'+name)
            method()
```

The `@property` decorator:

- wraps the methods in a `Descriptor` object and sets the following method as the object `__get__` method
- Adds  a `@method.setter` decorator, which will modify the `set` method
- Adds a `@method.deleter` decorator, which will modify the `delete` method

Another version, with better initialization and calculation (will allow subclassing more easily)

```python
class Leg:
  def __init__(self, rate=None, time=None, distance=None):
    # deque created first
    self._changes= deque(maxlen=2)
    self._rate= rate
    if rate: self._calculate('rate')
    self._time= time
    if time: self._calculate('time')
    self._distance= distance
    if distance: self._calculate('distance')

    def calc_distance(self):
        self._distance = self._time * self._rate

    def calc_time(self):
        self._time = self._distance / self._rate

    def calc_rate(self):
        self._rate = self._distance / self._time

    def _calculate(self, change):
        if change not in self._changes:
            self._changes.append(change)
        compute = {'rate', 'time', 'distance'} - set(self._changes)
        if len(compute) == 1:
            name = compute.pop()
            method = getattr(self, 'calc_'+name)
            method()
```

## Chapter 7: more advanced class design

wrap or extend?

> There's an important semantic issue here that we can also summarize as the wrap versus extend problem:
>
> - Do we really mean that the subclass is an example of the superclass? This is the is-a relationship.
> - Or do we mean something else? Perhaps there's an association, sometimes called the has-a relationship

- no method overloading, the latest implementation in a class override the definitions of the parents classes

- but calling the superclass is always possible by calling `super()`:

  ```python
   def some_method(self):
          # do something extra
          super().some_method()
  ```

- aggregation : objects wrapped have an existence out of the wrapper
- composition: objects wrapped don't have an independent existence
- inheritance: is-a relationship, subclassing the parent

> We generally focus on aggregation because removal of unused objects is entirely automatic. (reference counting)

### Separating concerns via multiple inheritance

(using superclass, mixin class); check `object.__class__.mro()` to see the method resolution order

```python
# main superclass
class Card:
    __slots__ = ('rank', 'suit')
    def __init__(self, rank, suit):
        super().__init__()
        self.rank = rank
        self.suit = suit
    def __repr__(self):
        return "{rank:2d} {suit}".format(
          rank=self.rank, suit=self.suit
        )

# main subclasses
class AceCard(Card):
    def __repr__(self):
        return " A {suit}".format(
            rank=self.rank, suit=self.suit
        )

class FaceCard(Card):
    def __repr__(self):
        names = {11: 'J', 12: 'Q', 13: 'K'}
        return " {name} {suit}".format(
            rank=self.rank, suit=self.suit,
            name=names[self.rank]
        )

# mixin superclass
class CribbagePoints:
    def points(self):
        return self.rank

# mixin subclass for J Q and K
class CribbageFacePoints(CribbagePoints):
    def points(self):
        return 10

# class definitions that combine the main classes
# and the mixin classes
class CribbageAce(AceCard, CribbagePoints):
    pass

class CribbageCard(Card, CribbagePoints):
    pass

class CribbageFace(FaceCard, CribbageFacePoints):
    pass

# factory
def make_card(rank, suit):
    if rank == 1: return CribbageAce(rank, suit)
    if 2 <= rank < 11: return CribbageCard(rank, suit)
    if 11 <= rank: return CribbageFace(rank, suit)

```

Mixins are used to add features to objects, for instance logging here; we've used `super().__init__()` to perform the `__init__()` method of any other classes defined in the MRO:

```python
class Logged:
  def __init__(self, *args, **kw):
    self.logger = logging.getLogger(self.__class__.__name__)
    super().__init__(*args, **kw)
  def points(self):
    p = super().points()
    self.logger.debug("points {0}".format(p))
    return p
```

Using it to build a new class, putting it first in the ancestors guarantees that the logging will be consistently applied:

```python
class LoggedCribbageAce(Logged, AceCard, CribbagePoints):
  pass
```

### Leveraging duck typing

> In the case of Python class relationships, if two objects have the same methods, and the same attributes, this has the same effect as having a common superclass. It works even if there's no common superclass definition other than the object class.
>
> it's sometimes easier to avoid a common superclass and simply check that two classes are both equivalent using the duck test—the two classes have the same methods and attributes, therefore, they are effectively members of some superclass that has no formal realization as Python code.

have a look at `___slots__`and `__dict__` to figure out the object's layout, and possible common superclass

Note inserting a class in a namespace (like the decimal type, part of Number witout a superclass) is possible

### Managing global and singleton objects

> One example of this is an implicit random number generating object that's part of the random module. When we evaluate random.random(), we're actually making use of an instance of the random.Random class that's an implicit part of the random module.
>
> There are two ways to do handle global state information.
>
> - One technique uses a module global variable because modules are singleton objects.
> - The other uses a class level (static) variable because a class definition is a singleton object

Defining a global variable doesn't need much explanation (put it in the module .py at the top level) ; here's how to make a class variable:

> We **didn't use self** in this example to make a point about variable assignment and instance variables. **When we use self.name on the right side of an assignment statement, the name may be resolved by the object, or the class, or any superclass**. This is the ordinary rule for searching a class.
>
> When we use self.name on the left side of assignment, that will create an instance variable. **We must use Class.name to be sure that a class-level variable is updated instead of creating an instance variable**.

```python
from collections import Counter
class EventCounter:
    _counts = Counter() # will be shared among all instances

# it would be possible to simply use the class variable
# but making accessors / methods is always better practice
# to not break encapsulation
def count(self, key, increment=1):
   EventCounter._counts[key] += increment
def counts(self):
    return EventCounter._counts.most_common()
```

> The alternative, of course, is to create a global object explicitly, and make it part of the application in some more obvious way. This might mean providing the object as an initialization parameter to objects throughout the application. This can be a fairly large burden in a complex application.

_Beware of abuse of this pattern!_

### Using more complex structures – maps of lists

```python
from collections import defaultdict
# Use the list function to populate the default value for defaultdict
module_details = defaultdict(list)
for row in data:
    module_details[row[2]].append(row) # append to default empty list
```

### Creating a class that has sortable / comparable objects

1. create a class
2. define its `__lt__`, `__gt__`, `__le__`, `__ge__`, `__eq__`, `__ne__` methods

See **Section 3.3 of the Python Language Reference** for other special methods that allow object customization

### Defining an ordered collection

Problem with long lists: complexity of searching is O(n), so very slow

to create a more efficient multiset:

- either use a mapping of `Counter` objects dict or other hash-based structure, meaning the ojects must be hashable

- or use a sorted list thanks to `bisect`, that will be much faster than a standard list; example:

  ```python
  def add(self, aCard: Card):
      bisect.insort(self.cards, aCard)
  def index(self, aCard: Card):
      i = bisect.bisect_left(self.cards, aCard)
      if i != len(self.cards) and self.cards[i] == aCard:
         return i
      raise ValueError
  ```

  can extend it to make it behave more like a `MutableSet`, check the docs (many more methods to define)

### Deleting from a list of mappings

recommendation: use shallow copies rather than list manipulation

## Chapter 8. Functional and Reactive Programming Features

### Writing generator functions with the yield statement

useful in particular when an argument may produce a result that does not fit in memory, so we use an iteration:

```python
import datetime
    def parse_date_iter(source): # argument must be an iterable
        for item in source:
            date = datetime.datetime.strptime(
                item[0],
                "%Y-%m-%d %H:%M:%S,%f")
            new_item = (date,)+item[1:]
            yield new_item
for item in parse_date_iter(data): # use the generator (=iterate)
  pprint(item)
```

an iterator acts like a while loop ending with a `StopIteration` exception.

using yield at the return of a function makes it an iterator (returning one value at a time)

Behind the scenes:

```python
for i in some_collection:
        process(i)
# something like this happens:
the_iterator = iter(some_collection)
try:
  while True:
    i = next(the_iterator)
    process(i)
  except StopIteration:
    pass
```

> The iterator concept can also be applied to functions. A function with a yield statement is called a generator function. It fits the template for an iterator. To do this, the generator returns itself in response to the iter() function. In response to the next() function, it yields the next value.

Note:

```python
p_10 = {i for i in range(2,200) if i % 10 == 0} # create a set
p_11 = [i for i in range(2,200) if i % 10 == 0] # create a list
p_12 = (i for i in range(2,200) if i % 10 == 0) # create a generator
```

writing higher level functions using the yield statement:

```python
def map(m, S):
  for s in S:
    yield m(s)
def filter(f, S):
  for s in S:
    if f(s):
      yield s
```

### Using stacked generator expressions

possible to chain generators, and create `z = g(f(x))` functions that use generators inside generators (a bit like recursion, minimal memory footprint, more like a pipeline)

**Sidenote**: stop using list indexes in processing, rather create a namespace, example:

```python
from types import SimpleNamespace

# This will produce an object that allows us to write row.date instead of row[0]
# An immutable namedtuple might be a better choice than a mutable SimpleNamespace
def make_namespace(merge_iter):
    for row in merge_iter:
      ns = SimpleNamespace(
        date = row[0],
        start_time = row[1],
        start_fuel_height = row[2],
        end_time = row[4],
        end_fuel_height = row[5],
        other_notes = row[7]
      )
      yield ns
```

### Applying transformations to a collection

can do it creating a generator `foo = (start_datetime(row) for row in tail_gen)` or by using `map()`:

```python
def parse_date(item):
  date = datetime.datetime.strptime(
    item[0],
    "%Y-%m-%d %H:%M:%S,%f")
  new_item = (date,)+item[1:]
  return new_item
# pass only the function name and the data (iterator or list) to map()
# the return of map() is an iterator
for row in map(parse_date, data):
  print(row[0], row[3])
```

### Picking a subset - three ways to filter

```python
def pass_non_date(row):
    return row[0] != 'date'
```

```python
# either use for statement
for item in collection:
    if pass_non_date(item):
        yield item
```

```python
# or generator expression
(item for item in data if pass_non_date(item))
```

```python
# or filter to apply the function
filter(pass_non_date, data)
```

```python
# even tighter using a lambda
filter(lambda item: not reject_date(item), data)
```

### Summarizing a collection – how to reduce

```python
from functools import reduce
def mul(a, b):
    return a * b
def prod(values):
    return reduce(mul, values, 1) # last argument is the base value

prod(range(1, 5+1))
# returns: 120
```

```python
# the factorial function is hence defined as:
def factorial(n):
    return prod(range(1, n+1))
```

The `reduce` function behaves as if it has this definition:

```python
def reduce(function, iterable, base):
    result = base
    for item in iterable:
        result = function(result, item)
    return result
```

When designing a reduce function we need to provide a binary operator, there are 3 ways to do this:

```python
def mul(a, b):
    return a * b

# or
add = lambda a, b: a + b
mul = lambda a, b: a * b

# or we can also import the definition from the operator module (and use it in the reduce call)
from operator import add, mul
```

### Minima and maxima

Slightly more complex since there's no base value

```python
def mymax(sequence):
    try:
        base = sequence[0]
        max_rule = lambda a, b: a if a > b else b
        reduce(max_rule, sequence, base)
    except IndexError:
        raise ValueError
```

> Note that a fold (or reduce() as it's called in Python) can be abused, leading to poor performance. We have to be cautious about simply using a reduce() function without thinking carefully about what the resulting algorithm might look like. In particular, the operator being folded into the collection should be a simple process such as adding or multiplying. Using reduce() changes the complexity of an O(1) operation into O(n).
>
> Imagine what would happen if the operator being applied during the reduction involved a sort over a collection. A complex operator—with O(n log n) complexity—being used in a reduce() would change the complexity of the overall reduce() to O(n2log n).

### Combining map and reduce transformations

Difficulty is iterators can only produce values once; typical pattern is using map to normalize (possibly using stacked generators), then filter, then reduce

If we want to have several reducers, we can't use a single iterator as it will consume all the data on the first pass, we can do various things:

```python
# wrap in a tuple, which is an iterator but not a generator
data = tuple(clean_data(row_merge(log_rows)))

# use tee() to create 2 copies of the iterable output
from itertools import tee
data1, data2 = tee(clean_data(row_merge(log_rows)), 2)
```

### Implementing "there exists" processing

> How can we write a process using generator functions that stops when the first value matches some predicate? How do we avoid for all and quantify our logic with there exists?

```python
def find_first(predicate, iterable):
    for item in iterable:
        if predicate(item):
            yield item
            break

import math
def prime(n):
    factors = find_first(
        lambda i: n % i == 0,
        range(2, int(math.sqrt(n)+1)) # only need to test until sqrt(n)
    )
    return len(list(factors)) == 0 # true/false
```

> This is different from a filter, where all of the source values will be consumed. When using the break statement to leave the for statement early, some of the source values may not be processed.
>
> In the **itertools** module, there is an alternative to this **`find_first()`** function. The **`takewhile()`** function uses a predicate function to keep taking values from the input. When the predicate becomes false, then the function stops processing values

```text
>>> from itertools import takewhile
>>> n = 13
>>> list(takewhile(lambda i: n % i != 0, range(2, 4)))
[2, 3]
```

### The itertools module

Check the following functions:

- `filterfalse()`
- `zip_longest()`
- `starmap()`
- `accumulate()`
- `chain()`
- `compress()`
- `dropwhile()`
- `groupby()`
- `islice()`
- `takewhile()`
- `tee()`

### Creating a partial function

```python
def standarize(mean, stdev, x):
    return (x - mean) / stdev

from types import SimpleNamespace

row_build = lambda rows: (SimpleNamespace(x=float(x), y=float(y)) for x,y in rows)

data_1 = list(row_build(text_parse(text_1)))
data_2 = list(row_build(text_parse(text_2)))

# we can go like this
import statistics
mean_x = statistics.mean(item.x for item in data_1)
stdev_x = statistics.stdev(item.x for item in data_1)

for row in data_1:
    z_x = standardize(mean_x, stdev_x, row.x)
    print(row, z_x)

for row in data_2:
    z_x = standardize(mean_x, stdev_x, row.x)
    print(row, z_x)

# to declutter we can also use partial(): specify 1 function, and 2 arguments to it
from functools import partial
z = partial(standardize, mean_x, stdev_x)

# or we can use a lambda the same way:
z = lambda x: standardize(mean_v1, stdev_v1, x)

# then for both:
for row in data_1:
    print(row, z(row.x))

for row in data_2:
    print(row, z(row.x))

```

> Both techniques create a callable object - a function - named z() that has the values for mean_v1 and stdev_v1 already bound to the first two positional parameters

Note the partial binds the actual values of the parameters, while the lambda will evaluate them at each call, so behaviour may be changed

Note the `reduce()` function doesn't lend itself to partials, because it doesn't support named argument values

functions can also return a function object as a result. This means that we can create a function like this:

```python
def prepare_z(data):
    mean_x = statistics.mean(item.x for item in data_1)
    stdev_x = statistics.stdev(item.x for item in data_1)
    return partial(standardize, mean_x, stdev_x)

z = prepare_z(data_1)
for row in data_2:
    print(row, z(row.x))
```

### Simplifying complex algorithms with immutable data structures

```python
from typing import *

def get(text: str) -> Iterator[List[str]]:
    for line in text.splitlines():
        if len(line) == 0:
            continue
        yield line.split()

from collections import namedtuple

DataPair = namedtuple('DataPair', ['x', 'y'])

def cleanse(iterable: Iterable[List[str]]) -> Iterator[DataPair]:
    for text_items in iterable:
    try:
        x_amount = float(text_items[0])
        y_amount = float(text_items[1])
        yield DataPair(x_amount, y_amount)
    except Exception as ex:
        print(ex, repr(text_items))
```

Can be replaced by

```python
DataPair = namedtuple('DataPair', ['x', 'y'])
RankYDataPair = namedtuple('RankYDataPair', ['y_rank', 'pair'])
PairIter = Iterable[DataPair]
RankPairIter = Iterator[RankYDataPair] # special case of Iterable

def rank_by_y(iterable: PairIter) -> RankPairIter:
    all_data = sorted(iterable, key=lambda pair:pair.y)
    for y_rank, pair in enumerate(all_data, start=1):
        yield RankYDataPair(y_rank, pair)
```

```text
>>> data = rank_by_y(cleanse(get(text_1)))
>>> pprint(list(data))
[RankYDataPair(y_rank=1, pair=DataPair(x=4.0, y=4.26)),
 RankYDataPair(y_rank=2, pair=DataPair(x=7.0, y=4.82)),
 RankYDataPair(y_rank=3, pair=DataPair(x=5.0, y=5.68)),
 ...,
 RankYDataPair(y_rank=11, pair=DataPair(x=12.0, y=10.84))]
 ```

> Creating new objects can - in many cases - be more expressive of the algorithm than changing the state of objects
>
> The typing module includes an alternative to the namedtuple() function: NamedTuple(). This allows specification of a data type for the various items within the tuple. It looks like this:

```python
DataPair = NamedTuple('DataPair', [
        ('x', float),
        ('y', float)])
```

### Writing recursive generator functions with the yield from statement

```python
def find_path(value, node, path=[]):
    if isinstance(node, dict):
        for key in node.keys():
            yield from find_path(value, node[key], path+[key])
    elif isinstance(node, list):
        for index in range(len(node)):
            yield from find_path(value, node[index], path+[index])
    else:
        if node == value:
            yield path
```

The yield from X statement is shorthand for: `for item in X: yield item`

example: find all divisors for a number:

```python
def factor_iter(x):
    limit = int(math.sqrt(x)+1)
    for n in range(2, limit):
        q, r = divmod(x, n)
        if r == 0:
            yield n
            yield from factor_iter(q)
            return
    yield x
```

## Chapter 9. Input/Output, Physical Format, and Logical Layout

### Using pathlib to work with filenames

```python
from pathlib import Path
input_path = Path(options.input)
output_path = input_path.with_suffix('.out')
input_directory = input_path.parent
input_stem = input_path.stem
output_stem_pass = input_stem + "_pass"
# note the / operator assembles the path
output_path = (input_directory / output_stem_pass).with_suffix('.csv')

output_parent = input_path.parent / "output"
input_stem = input_path.stem
output_path = (output_parent / input_stem).with_suffix('.src')

# checking mtime
file1_path.stat().st_mtime

# deleting a file
try:
    input_path.unlink()
except FileNotFoundError as ex:
    print("File already deleted")

# finding files
directory_path = Path(options.file1).parent
list(directory_path.glob("ch08_r*.py"))
```

### Reading and writing files with context managers

```python
from pathlib import Path
summary_path = Path('summary.dat')
with summary_path.open('w') as summary_file:
    ...
```
