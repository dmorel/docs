# Python packaging

**Note: all this can easily be done with poetry**

## 1. make a directory structure 

example for the _sample_ module, from <http://docs.python-guide.org/en/latest/writing/structure/>

```bash
README.rst
LICENSE
setup.py
requirements.txt
sample/__init__.py
sample/core.py
sample/helpers.py
docs/conf.py
docs/index.rst
tests/test_basic.py
tests/test_advanced.py
```

## 2. make a python setup file

```python
from setuptools import setup, find_packages

setup(name='m6',
      version='0.1',
      description='M6 Utilities',
      url='http://foo.bar',
      author='David Morel',
      author_email='david.morel@amakuru.net',
      license='MIT',
      packages=find_packages(),
      install_requires=[
          'airflow',
      ],
      zip_safe=False)
```

## 3. run  `python setup.py sdist`

create the source distribution (tarball) in dist/

## 4. `python setup.py bdist_wheel`

also, create a wheel (built package, faster install, possibly architecture dependent)

## 5. optionally use twine to upload the distribution

`twine upload dist/*` <https://packaging.python.org/key_projects/#twine>

## Produce a python index from git or something else, the quick and simple way

- no need to upload to an index server, _pip_ can install from git, for instance

- create a requirements.txt file listing various packages, see <http://stackoverflow.com/questions/4830856/is-it-possible-to-use-pip-to-install-a-package-from-a-private-github-repository/39254921> look for "requirements.txt"
    - taken from github
    - taken from pypi
    - etc

- Use a simple static webserver, serving the tree produced by pip2pi based on the requirements file:
      - <https://github.com/wolever/pip2pi>
      - `pip2pi ~/Sites/packages/ -r requirements.txt`
    
- Alternative solution: use pypiserver (pypiserver - minimal PyPI server for use with pip/easy_install) <http://pypiserver.readthedocs.io/en/latest/> which bundles its webserver

