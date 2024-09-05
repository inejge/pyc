## pyc - Python -c command line parameter translator

Python, as delivered, is not well suited for executing all kinds of scripts
given on the shell command line. There is the `-c` command-line switch, but its
utility is limited if you want to use imperative constructs and keep everything
on a single line, without here documents or piping the scripts to the interpreter.
Simple programs will work, but any block can't have preceding statements, limitng
the range of expressible scripts.

This script accepts a Python program given as the first argument, written with
a slightly extended `-c` syntax, and outputs a Python `exec()` function executing
the program, suitable for using with shell command substitution. In the input,
every `;` is substituted with a newline, while every `;;` denotes a newline
followed by a one-level decrease in indentation.

Indentation level is automatically increased if a block-introducing colon is
recognized. To prevent a key in a dictionary initializer from being parsed as
the beginning of a block, separate the key and the colon with a space.

Depending on the shell quoting flavor, the Python program may be subject to shell
variable expansion, and any embedded double quotes and backslashes will be further
escaped to make it work with `exec()`. The command substitution syntax used must be
`$()`&mdash;backticks won't work. The whole substitution should be double-quoted.

Consider the following program, implementing the bare-bones `echo` command:

```python
import sys

first = True
for a in sys.argv[1:]:
    if not first:
        print(" ", end="")
    print(a, end="")
    first = False
print("")
```

It can be transformed and invoked by:

```
python3 -c "$(pyc 'import sys; first = True; for a in sys.argv[1:]: if not first: print(" ", end="");; print(a, end=""); first = False;; print("")')"
```

### License

Copyright (c) 2024 Ivan Nejgebauer

Licensed under Apache License, Version 2.0 ([LICENSE](LICENSE))
