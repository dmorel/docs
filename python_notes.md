# Python Notes

[TOC]

## List of curated resources

<https://github.com/vinta/awesome-python>

## Yield, generators and iterators

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

```text
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

## list comprehensions

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

## Introspection

From <http://stackoverflow.com/questions/1006169/how-do-i-look-inside-a-python-object>

Look at type(), dir(), id(), getattr(), hasattr(), globals(), locals(), callable()

built-in fucntions "type() and dir() are particularly useful for inspecting the type of an object and its set of attributes, respectively."

Also check <https://docs.python.org/2/library/inspect.html> on 2.7+

## Debug with pdb

From <http://sametmax.com/debugger-en-python-les-bases-de-pdb/>

- add `import pdb; pdb.set_trace()` where you need the program to stop and give you a command prompt
- usual list of commands: "l", "n", "s", "r", "q", "c".
- command "unt" : finish running a loop (normally) and return, giving back a prompt
- will fail on threads and in some similar conditions
- much better: **ipdb** (pdb + ipython), same way: `import ipdb; ipdb.set_trace()`

## Making code run under python 2 and python 3

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

## Serve packages for pip

From <http://python-guide-pt-br.readthedocs.io/en/latest/shipping/packaging/>

- make a simple tree and run `python -m SimpleHTTPServer <port>` in it, then `pip install --extra-index-url=http://127.0.0.1:<port>/ <packagename>`
- use pip2pi and (r)sync to destination, then use `pip install --index-url=<destination>` (could be http:// or file:///, or whatever)

## Update all outdated packages

<http://stackoverflow.com/a/3452888>

```shell
pip freeze --local | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U
```

or 

```shell
pip list --outdated --format=freeze ...
```

## Sort lists

The method list.sort() is sorting the list in place, and as all mutating
methods it returns None. Use the built-in function sorted() to return a new
sorted list.

```python
result = sorted((trans for trans in my_list if trans.type in types), key=lambda x: x.code)
```

Instead of lambda x: x.code, you could also use the slightly faster
`operator.attrgetter("code")`

## Flatten list of lists and turn into a set

```python
all_keys = set(
    [key for dag_details in get_dags_details(dagbag) 
        for key in dag_details.get("default_args",None).keys()
    ])
```

## Iterate over list elements or keys

```python
for key, value in d.items(): # python3
```

## Find out if a dictionary contains a key

```python
if "foo" in mydict: # checks mydict contains a "foo element"
```

## Retrieve output of a command

```python
dfsadmin = subprocess.check_output(['/usr/bin/hdfs', 'dfsadmin', '-report'])
capacity = re.search('Present Capacity: ([0-9]+)', dfsadmin).group(1)
```

## Scrape Twitter

<https://twython.readthedocs.io/en/latest/api.html>

Locations for which trends are available:
<https://dev.twitter.com/rest/reference/get/trends/available> then retrieve
trend for place: <https://dev.twitter.com/rest/reference/get/trends/place>

Introduction:
<https://codeandculture.wordpress.com/2016/01/19/scraping-twitter-with-python/>

Twitter app for scraping: <https://apps.twitter.com/app/13428400>

## Turn JSON to an HTML table

<https://pypi.python.org/pypi/json2html>

## Regex replace with code execution

```python
import re
import hashlib
chaine_test = "0|Non|Inconnu|438693739|399369822|lololouis3@gmail.com|2017-02-1"
print re.sub('^(([^|]*\|){5})\s*([^|]+@[^|@\s]+)\s*(.*)', \
    lambda x: "{}{}{}".format(x.group(1), \
        hashlib.md5(x.group(3).lower().encode('utf-8')).hexdigest(), \
        x.group(4)), chaine_test)
```

## Repeatable hashing for experiments

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

## Merging two dictionaries

From <https://stackoverflow.com/a/26853961> in python 3.5 simply do:

```
z = {**x, **y}
```

## Creating and adding to sets

```
In [2]: foo=set([1,2,3])
Out[3]: {1, 2, 3}
In [4]: foo.update([2,3,4])
Out[5]: {1, 2, 3, 4}
```

## Padding strings or numbers with zeros or something else

From <https://stackoverflow.com/a/339013>

python 3, numbers:

```
print('{0:03d} {1:05d}'.format(n,m))
```

padding strings:

```python
print(t.rjust(10, '0'))
print("foo".center(42,"-"))
```

## Secure passwords

From <https://www.cyberciti.biz/python-tutorials/securely-hash-passwords-in-python/>

```python
from passlib.hash import pbkdf2_sha256
hash = pbkdf2_sha256.encrypt("password", rounds=200000, salt_size=16)
pbkdf2_sha256.verify("password", hash)
```

## Retrieve path of the current file

From: <https://stackoverflow.com/questions/247770/retrieving-python-module-path>

## Creating Pandas DataFrames from Lists and Dictionaries

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

## Working with bits

Use bitstring: <https://pythonhosted.org/bitstring/walkthrough.html>

## Working with binary data

See: <https://www.devdungeon.com/content/working-binary-data-python>

Turn string into bytes:

```python
mystring.encode()
```

## Regexes

Find all table names in a mysql dump file

```python
table_name = re.findall(r'^\s*CREATE TABLE\s+(?:IF NOT EXISTS\s+)?`?([^\s\`]+)',line,re.I)[0]
```

## Avoid serialization errors in JSON

```python
json.dumps(my_dictionary, indent=4, sort_keys=True, default=str)
```

Avoid serialization issues with datetime types, among others, see: <https://stackoverflow.com/a/36142844>

## Pass function as an argument to a function

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

## Create and load virtual environments with pyenv-virtualenv

```bash
## install pyenv
brew install pyenv 
brew install pyenv-virtualenv

## add these 2 lines to .bashrc or .zshrc
## second line is to source the virtualenvs automatically using pyenv
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"

## install a specific python version
pyenv install 3.7.5

## create a virtualenv
pyenv virtualenv 3.7.5 my_custom_venv

## go to the source directory for the project and point pyenv to the created venv
cd <project dir>
pyenv local my_custom_venv

## now run all installs with pip that are needed, and use the virtualenv's python
```

**Update: not using this anymore, switch to poetry, better**

## Run managed processes as daemons

Several daemon libs available, but heavier-duty tools available, à la `init`:

- <http://supervisord.org/index.html>
- <https://mmonit.com/monit/>
