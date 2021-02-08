[toc]

# Bits and pieces

### List of curated resources

<https://github.com/vinta/awesome-python>

### Yield, generators and iterators

<http://sametmax.com/comment-utiliser-yield-et-les-generateurs-en-python/>

yield is like return, but returns a generator object (an iterator). This object can be used like a normal iterable (can only be queried once per value, etc)

```python
def creerGenerateur() :
    mylist = range(3)
    for i in mylist:
    yield i*i
generateur = creerGenerateur()
for i in generateur:
     print(i)
```

to make it easier to work with iterables (strings, lists, even sets) look at the `itertools` module, offering map, zip, slice, islice, chain, etc.

an iterator can be obtained by using the iter() built-in on pretty much anything: 

```
>>> iter([1, 2, 3])
< listiterator object at 0x7f58b9735dd0>
>>> iter((1, 2, 3))
< tupleiterator object at 0x7f58b9735e10>
>>> iter(x*x for x in (1, 2, 3))
< generator object  at 0x7f58b9723820>
>>> gen = iter([1, 2, 3])
>>> gen.next()
1
```

will throw a `StopIteration` exception beyond the last value

Also see: <https://wiki.python.org/moin/Generators>

### list comprehensions

<http://sametmax.com/python-love-les-listes-en-intention-partie/>

<http://sametmax.com/python-love-les-listes-en-intention-partie-2/>

```python
sequence = ["a", "b", "c"]
new_sequence = [element.upper() for element in sequence]
```

```python
>>> nombres = range(10)
>>> print([nombre for nombre in nombres if nombre % 2 == 0])
>>> print([sum(range(nombre)) for nombre in range(10) if nombre % 2 == 0])
[0, 1, 6, 15, 28]
>>> [sum(range(nombre)) for nombre in range(0, 10, 2)] # better :)
[0, 1, 6, 15, 28]
```

### Introspection

From <http://stackoverflow.com/questions/1006169/how-do-i-look-inside-a-python-object>

Look at type(), dir(), id(), getattr(), hasattr(), globals(), locals(), callable()

built-in fucntions "type() and dir() are particularly useful for inspecting the type of an object and its set of attributes, respectively."

Also check <https://docs.python.org/2/library/inspect.html> on 2.7+

### Debug with pdb

From <http://sametmax.com/debugger-en-python-les-bases-de-pdb/>

- add `import pdb; pdb.set_trace()` where you need the program to stop and give you a command prompt
- usual list of commands: "l", "n", "s", "r", "q", "c".
- command "unt" : finish running a loop (normally) and return, giving back a prompt
- will fail on threads and in some similar conditions
- much better: **ipdb** (pdb + ipython), same way: `import ipdb; ipdb.set_trace()`

### Making code run under python 2 and python 3

- <http://python3porting.com/noconv.html>
    - easier to port 3 to run under 2 than the other way around
    - first run 2to3 on your code, fix the print() related errors which will be the bulk of it
    - `from __future__ import print_function` gives a python-3 compatible syntax in python 2
    - exceptions:
        - python 2: `except ZeroDivisionError, e:` e is a variable that captures the exception
        - python 2: `except (ZeroDivisionError, TypeError):` use parens to capture several types
          - python 3 and 2: `except (ZeroDivisionError, TypeError) as e:`
    - imports: some modules have changed, use imports in a try/catch block, with this: `except ImportError:`
    - imports: 2to3 fixes most of the issues; use the generated code in the try/catch to handle both versions
    - integers: int and long have been merged, `1L` is a syntax error in python 3, use this:
    
            import sys
            if sys.version_info > (3,):
                long = int
            "1L"
        
    - octal literals: not 0... anymore but 0o...
    - unicode: 2 to 3 converts all u'' strings to straight strings, but u'' is back in python 3.3 anyway (only an issue in 3.1 and 3.2)
    - for most of these cases and more, check <http://pypi.python.org/pypi/six> to make it easier
  
- <https://docs.python.org/2/library/2to3.html> has many options, check it out

### Serve packages for pip

From <http://python-guide-pt-br.readthedocs.io/en/latest/shipping/packaging/>

- make a simple tree and run `python -m SimpleHTTPServer <port>` in it, then `pip install --extra-index-url=http://127.0.0.1:<port>/ <packagename>`
- use pip2pi and (r)sync to destination, then use `pip install --index-url=<destination>` (could be http:// or file:///, or whatever)

### Update all outdated packages

<http://stackoverflow.com/a/3452888>
`pip freeze --local | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U`
or `pip list --outdated --format=freeze ...`

### Sort lists

The method list.sort() is sorting the list in place, and as all mutating
methods it returns None. Use the built-in function sorted() to return a new
sorted list.

```python
result = sorted((trans for trans in my_list if trans.type in types), key=lambda x: x.code)
```

Instead of lambda x: x.code, you could also use the slightly faster
`operator.attrgetter("code")`

### Flatten list of lists and turn into a set

```python
all_keys = set(
    [key for dag_details in get_dags_details(dagbag) 
        for key in dag_details.get("default_args",None).keys()
    ])
```

### Iterate over list elements or keys

```python
for key, value in d.items(): # python3
```

### Find out if a dictionary contains a key

```python
if "foo" in mydict: # checks mydict contains a "foo element"
```

### Retrieve output of a command

```python
dfsadmin = subprocess.check_output(['/usr/bin/hdfs', 'dfsadmin', '-report'])
capacity = re.search('Present Capacity: ([0-9]+)', dfsadmin).group(1)
```

### Scrape Twitter

<https://twython.readthedocs.io/en/latest/api.html>

Locations for whihch trends are available:
<https://dev.twitter.com/rest/reference/get/trends/available> then retrieve
trend for place: <https://dev.twitter.com/rest/reference/get/trends/place>

Introduction:
<https://codeandculture.wordpress.com/2016/01/19/scraping-twitter-with-python/>

Twitter app for scraping: <https://apps.twitter.com/app/13428400>

### Turn JSON to an HTML table

<https://pypi.python.org/pypi/json2html>

### Regex replace with code execution

```python
import re
import hashlib
chaine_test = "0|Non|Inconnu|438693739|399369822|lololouis3@gmail.com|2017-02-1"
print re.sub('^(([^|]*\|){5})\s*([^|]+@[^|@\s]+)\s*(.*)', \
    lambda x: "{}{}{}".format(x.group(1), \
        hashlib.md5(x.group(3).lower().encode('utf-8')).hexdigest(), \
        x.group(4)), chaine_test)
```

### Repeatable hashing for experiments

```python
from __future__ import print_function
import hashlib;
import random;
md5 = hashlib.md5()
variants=("abcdef")
for i in range(1,10000):
    md5.update("%s__%s".format("exp-01", random.randint(100000000,999999999)).encode("utf-8"))
    variant = int(md5.hexdigest(),16) % 100
    print(variants[variant], end='')
```

### Merging two dictionaries

From <https://stackoverflow.com/a/26853961> in python 3.5 simply do:

```
z = {**x, **y}
```

### Creating and adding to sets

```
In [2]: foo=set([1,2,3])
Out[3]: {1, 2, 3}
In [4]: foo.update([2,3,4])
Out[5]: {1, 2, 3, 4}
```

### Padding strings or numbers with zeros or something else

From <https://stackoverflow.com/a/339013>

python 3, numbers

```
print('{0:03d} {1:05d}'.format(n,m))
```

padding strings:

```
print(t.rjust(10, '0'))` `print("foo".center(42,"-"))
```

### Secure passwords

From <https://www.cyberciti.biz/python-tutorials/securely-hash-passwords-in-python/>

```python
from passlib.hash import pbkdf2_sha256
hash = pbkdf2_sha256.encrypt("password", rounds=200000, salt_size=16)
pbkdf2_sha256.verify("password", hash)
```

### Retrieve path of the current file

From: <https://stackoverflow.com/questions/247770/retrieving-python-module-path>

### Creating Pandas DataFrames from Lists and Dictionaries

From: <http://pbpython.com/pandas-list-dict.html>

From psycopg2 resultset (list of lists) in a table:

```python
import pandas
df = pd.DataFrame.from_records(
    final, 
    columns=['name','schema','table','consolidated_schema','consolidated_table'])
pd.options.display.max_rows = 100000
df.to_sparse()
```

From a simple list (with a generator to find OR values in 2 lists here):

```python
only_in_tables=[e for e in rs_tables_flat if e not in outputs_flat]
df = pd.DataFrame(only_in_tables) # just a list in the constructor
pd.options.display.max_rows = 100000
pd.options.display.max_colwidth=1000
df.to_sparse()
```

### Working with bits

Use bitstring: <https://pythonhosted.org/bitstring/walkthrough.html>

### Working with binary data

See: <https://www.devdungeon.com/content/working-binary-data-python>

Turn string into bytes:

```python
mystring.encode()
```

### Regexes

Find all table names in a mysql dump file

```python
table_name = re.findall(r'^\s*CREATE TABLE\s+(?:IF NOT EXISTS\s+)?`?([^\s\`]+)',line,re.I)[0]
```

### Avoid serialization errors in JSON

```python
json.dumps(my_dictionary, indent=4, sort_keys=True, default=str)
```

Avoid serialization issues with datetime types, among others, see: <https://stackoverflow.com/a/36142844>

### Pass function as an argument to a function

Functions are first-class citizens, handy:

```python
def myfunc1(baz)
    print('foo', baz)

def myfunc2(baz)
    print('bar', baz)

def func(my_func, *args):
    res = my_func(args[0])
    return res

print(func(myfunc1, 'whatever'))
```

# From Modern Python Cookbook (Packt Publishing)

## Chapter 1. Numbers, Strings, and Tuples

### decimal (for currencies):

    >>> from decimal import Decimal
    >>> tax_rate = Decimal('7.25')/Decimal(100)
    >>> purchase_amount = Decimal('2.95')
    >>> tax_rate * purchase_amount
    Decimal('0.213875')

### binary

    >>> composite_byte = 0b01101100
    >>> bottom_6_mask =  0b00111111
    >>> bin(composite_byte >> 6)
    '0b1'
    >>> bin(composite_byte & bottom_6_mask)
    '0b101100'

### fractions

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

### floats, rouding

    >>> answer= (19/155)*(155/19)
    >>> round(answer, 3)
    1.0

### the math module

    >>> (19/155)*(155/19) == 1.0
    False
    >>> math.isclose((19/155)*(155/19), 1)
    True

_math.fsum()_ better than _sum()_
explore other _math.*_ functions
complex numbers: _cmath_ module

### integer division

    >>> total_seconds = 7385
    >>> hours = total_seconds//3600
    >>> remaining_seconds = total_seconds % 3600

or

    >>> total_seconds = 7385
    >>> hours, remaining_seconds = divmod(total_seconds, 3600)

note: using new division operators like // need `>>> from __future__ import
division` in python 2

### string operations

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

We made use of slice notation to decompose a string. A slice has two parts:
[start:end]. A slice always includes the starting index. String indices always
start with zero as the first item. It never includes the ending index.

parsing numbers:

    >>> '1298'.isnumeric()
    True

regex parsing

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

regexes can span several lines

    >>> ingredient_pattern = re.compile(
    ... r'(?P<ingredient>\w+):\s+' # name of the ingredient up to the ":"
    ... r'(?P<amount>\d+)\s+'      # amount, all digits up to a space
    ... r'(?P<unit>\w+)'           # units, alphanumeric characters
    ... )

format strings

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

process string as a list

    >>> title_list = list(title)
    >>> colon_position = title_list.index(':')
    >>> ...
    >>> title = ''.join(title_list)

unicode characters

    \uxxxx     for 4 hex digits
    \Uxxxxxxxx for 8 hex digits
    \N{UNICODE_NAME}
    
    # display raw strings
    >>> r"\w+"
    '\\w+'

encoding strings

- to specify an ecnoding for I/O globally: `export PYTHONIOENCODING=UTF-8 `
- to specify an ecnoding per FH:

```
with open('some_file.txt', 'w', encoding='utf-8') as output:
    print( 'You drew \U0001F000', file=output )
```

- when opening a file in byte mode:

```
>>> string_bytes = 'You drew \U0001F000'.encode('utf-8')
>>> string_bytes
b'You drew \xf0\x9f\x80\x80'
```

- when retrieving a stream of bytes with urllib for instance:

```
>>> document = forecast_text.decode("UTF-8")
>>> document[:80]
'&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.or"&gt;'
```

The b prefix is gone. We've created a proper string of Unicode characters from the stream of bytes.

NOTE: for web scraping, use <https://www.crummy.com/software/BeautifulSoup/>

### using tuples

the result of a regex match groups() method is a tuple

create a tuple: `foo = ("bar", "baz", 2.0)`

singleton tuple: `'355,' -> returns (355,), not 355)`

```
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

```
>>> from math import (sin, cos, tan,
...    sqrt, log, frexp)
```

### Docstrings

use ReStructuredText (RST) markup (see details in the book, 2 paragraphs)

RST spec: <http://docutils.sourceforge.net/>

    #!/usr/bin/env python3
    '''
    My First Script: Calculate an important value.
    '''

type inference (not built-in, just advisory): `color = 355/113 # type: float`

### Complex if statements

use assert to verify a complex condition, throws AssertionError if false

use while loop and assertion:

```
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
`raise ExceptioType(...) from exception` raises a chained exception

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

```
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

```
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

Move actions in `def ... :` statements, then, if it should run as a standalone script *and* a library, add this at the end:

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

Careful, a list is addressed by reference, so to make a copy of it, use `list1.copy()`; this is why we use `fields_copy2 = fields[::-1] ` which is a shallow copy, saving 1 instruction

### Using set methods and operators

We can build a set using the `set()` function to convert an existing collection to a set, and we can add to it using the `add()` method, and use the `update()` method, the union operator `|` or the set's `union` method.

```python
collection.add(item) 			# mutates, single item
colection.update({item, ...}) 	# mutates, multiple items
collection.union({item}) 		# returns a set, doesn't mutate
collection | {item, ...}      	# returns a set
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
matches - to_be_ignored 			# {'IP: 111.222.111.222'}
matches.difference(to_be_ignored) 	# {'IP: 111.222.111.222'}
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

`def gather_stats(n, samples=1000, summary=Counter()): `

this is a really bad idea, because on the next iteration with no summary passed, it will reuse the summary variable which is already initialized in the scope. better do this:

```python
def gather_stats(n, samples=1000, summary=None):
	  if summary is None: summary = Counter()
    ...
```

*Don't use mutable defaults for functions. A mutable object (*set*,* list*,* dict*) should not be a default value for a function parameter.*

```python
def gather_stats(n, samples=1000, summary_func=lambda x:Counter(x)): 
    summary = summary_func( 
      sum(randint(1,6) for d in range(n)) for _ in range(samples)) 
    return summary
  
gather_stats(2, 12, summary_func=list)  # returns a list
gather_stats(2, 12)              				# returns a Counter
```

## Chapter 5: User Inputs and Outputs

foobar
