# Fn Graph

Manage, maintain and reuse complex function graphs without the hassle.

## Installation

```
pip install fn_graph
```

## Outline of the problem

In complex domains, like financial modelling or data-science, modellers and programmers often have complex logic that needs to be re-used in multiple environments. For example a financial analyst may write a model in a notebook, which then must be placed in a production environment (normally through rewriting the notebook which was not written in a modular or easy to use manner), after which changes need to made, which must be done in the notebook environment, which then need to be moved back into production. This is the stuff of [nightmare fuel](https://www.urbandictionary.com/define.php?term=nightmare%20fuel) for everyone involved.

To make this process easier the best option is to break functionality into small reusable functions, that can be referenced in both the notebook environment and the production environment. Now you unfortunately you have this bag of small functions whose relationships you have to manage. There are few ways to do this. Assume we have the functions below:

```python
def get_a():
    return 5

def get_b(a):
    return a * 5

def get_c(a, b):
    return a * b
```

**Option 1 - Directly compose at call site:**

```python
a = get_a()
b = get_b(a)
c = get_c(a, b)
```

_Pros:_ The modeller can easily see intermediate results\
_Cons:_ Now this potentially complex network of function calls has to be copied and pasted between notebooks and production

**Option 2 - Wrap it up in a function that pass around**

```python
def composed_c():
    a = get_a()
    b = get_b(a)
    return  get_c(a, b)
```

_Pros:_ This is easy to reference from both the notebook and production\
_Cons:_ The modeller cannot see the intermediate results, so it can be difficult to debug (this is a big problem if you are working with big multi-dimensional objects that you may want to visualize, a debugger does not cut it)

**Option 3 - Directly call functions from each other**

```python
def get_a():
    return 5

def get_b(a):
    return get_a() * 5

def get_c():
    return get_a() * get_b()
```

_Pros:_ This is easy to reference from both the notebook and production\
_Cons:_ The modeller cannot see the intermediate results and functions cannot be reused.

None of these are great. Fn Graph would solve it like this.

```python
from fn_graph import Composer

def a():
    return 5

def b(a):
    return a * 5

def c(a, b):
    return a * b

composer = Composer().update(a, b, c)

# Call any result
composer.c() # 125
composer.a() # 5

composer.graphviz()
```

![Graph of composer](intro.gv.png)

The composer can then be easily passed around in both the production and notebook environment. It can do much more than this.

## Features

- Manage complex function graphs, including using namespaces.
- Update composers to gradually build more and more complex logic.
- Enable incredible function reuse.
- Visualize logic to make knowledge sharing easier.
- Perform graph operations on composers to dynamically rewire your logic
- Manage calculation life cycle, with hooks, and have access to all intermediary calculations.

**Coming soon:**

- Intelligent Caching

## Similar projects

**Airflow**

Airflow is a task manager. It is used to run a series of generally large tasks in an order that meets their dependencies, potentially over multiple machines. It has a whole scheduling and management apparatus around it. Fn Graph is not trying to do this. Fn Graph is about making complex logic more manageable. You may well want to use Fn Graph inside your airflow tasks.

**d6tflow**

TBD

**Luigi**

TBD

**Tensorflow, or PyTorch, or Scikit-Learn**

TBD
