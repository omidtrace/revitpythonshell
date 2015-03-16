# Introduction #

As of [r97](https://code.google.com/p/revitpythonshell/source/detail?r=97), RPS can now automatically complete member access in the interactive shell. Just press `TAB` while writing code and a list of possible completions will appear. Select a completion and press `ENTER` to accept a completion or press `ESC` to close the dialog without changes.

# Details #

How does it work? I was surprised at how little magic was needed to implement this feature. Basically, the python standard library already ships with a module called [rlcompleter](http://docs.python.org/library/rlcompleter.html). It is intended for the `readline` functionality not present on windows machines (though you can get it to work, I think), but can also be used separatly as in the case of RPS.

When you press `TAB` in an interactive RPS shell, the `__main__` module is searched for a function called `__completer__` which accepts a string to be completed and returns a list of possible completions. This list is then shown in the dropdown list and the text at the prompt is replaced once a completion is chosen.

If no `__completer__` function is defined, then nothing happens.

Here is an example of the `__completer__` function, as shipped with [r97](https://code.google.com/p/revitpythonshell/source/detail?r=97):

```
# hook for autocompletion functionality
# HINT: modify only if you understand the autocompletion functionality inner workings...
def __completer__(uncompleted):
    from rlcompleter import Completer
    from traceback import format_exc
    import __builtin__
    try:
        __builtin__.__dict__.keys() # BUGFIX for rlcompleter.py, first invocation of global_matches() will otherwise fail in py2.5
        completer = Completer()
        i = 0
        result = []
        completion = completer.complete(uncompleted, i)
        while completion:
            result.append(completion)
            i += 1
            completion = completer.complete(uncompleted, i)
        return result
    except:
        return [format_exc()]
```