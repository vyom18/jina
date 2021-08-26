# Executor Usage

```{toctree}
:hidden:

executor-api
structure
```

`Executor` process `DocumentArray` in-place via functions decorated with `@requests`.

- An `Executor` should subclass directly from `jina.Executor` class.
- An `Executor` class is a bag of functions with shared state (via `self`); it can contain an arbitrary number of
  functions with arbitrary names.
- Functions decorated by `@requests` will be invoked according to their `on=` endpoint.



## Minimum working example

### Pure Python

```python
from jina import Executor, Flow, Document, requests


class MyExecutor(Executor):

    @requests
    def foo(self, **kwargs):
        print(kwargs)


f = Flow().add(uses=MyExecutor)

with f:
    f.post(on='/random_work', inputs=Document(), on_done=print)
```

### With YAML

`MyExecutor` described as above. Save it as `foo.py`.

`my.yml`:

```yaml
jtype: MyExecutor
metas:
  py_modules:
    - foo.py
  name: awesomeness
  description: my first awesome executor
requests:
  /random_work: foo
```

In this example your executor is defined in a single python file, `foo.py`. If you want to use multiple python files
(for example, `foo.py` and `helper.py`), check out how to organize them in the section [Structure of the Repository](#structure-of-the-repository)

Construct `Executor` from YAML:

```python
from jina import Executor

my_exec = Executor.load_config('my.yml')
```

Flow uses `Executor` from YAML:

```python
from jina import Flow, Document

f = Flow().add(uses='my.yml')

with f:
    f.post(on='/random_work', inputs=Document(), on_done=print)
```



