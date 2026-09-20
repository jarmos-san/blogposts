---
title: Building CLI applications with Python's argparse module.
timestamps:
  publishedOn: 2025-11-12T16:50:04+00:00
description: |
  A step-by-step guide to creating Python CLI applications with argparse. Parse
  arguments, generate files, and automate tasks using only the standard library.
status: published
coverImage:
  url: https://ik.imagekit.io/jarmos/building-cli-apps-with-argparse.png?updatedAt=1763202236560
  alt: Building CLI apps with Python standard library modules
sitemap:
  loc: /blogs/building-cli-apps-with-argparse-from-the-standard-library
  lastmod: 2025-11-12T16:50:04+00:00
  changefreq: yearly
  priority: 1
status: published
---

If you've automated repetitive tasks with a Python script that has grown more
complex over time, you've probably considered turning it into a CLI tool for
easier sharing. I'm in the same boat right now, exploring the best ways to
create and distribute these scripts!

While CLI frameworks like [Typer](https://typer.tiangolo.com/) and
[Click](https://click.palletsprojects.com/en/stable/) exist, I prefer not to
rely on third-party dependencies for simple applications. Avoiding extra
dependencies keeps the script portable since it only needs the Python
interpreter installed and ready to use. That's why the `argparse` module from
Python's standard library is perfect for my needs.

This article is a brief guide to the `argparse` module from the Python standard
library. For hands-on experience building real-world projects, I'll share
implementation details of my
[mkblog](https://github.com/Jarmos-san/dotfiles/blob/main/dotfiles/.local/bin/mkblog)
script. I use this script to generate boilerplate Markdown files from a
template, automating the process of creating new blog posts on my website. On a
side note, it is **STRONGLY** recommended to read the official documentation of
the module to learn more about it. This article only briefly touches over some
of the important mechanisms of the module and it should be enough for most
generic everyday use cases.

**NOTE**: The instructions and code in this post are written for Unix-like
systems (Debian/Ubuntu, macOS, etc.). There's no guarantee of similar behavior
when running the code on other systems, especially Windows.

## Understanding Python's CLI Argument Parsing

If you check out the `mkblog` script linked above, you'll notice a few things:

1. The script has no extension-Unix-like systems don't require one.
2. The [shebang](<https://en.wikipedia.org/wiki/Shebang_(Unix)>) line at the top
   points to the Python interpreter that executes the script.
3. Scroll to the bottom and you'll see this conditional statement:

```python [mkblog]
if __name__ == "__main__":
    main()
```

This statement is a standard practice in the Python community-it defers
execution of an "entrypoint" function to the script. The exact details deserve a
separate blog post, so I'll skip them for now.

Before proceeding, create a file named `mkblog` (without any file extension) and
follow along for hands-on experience. Once you've created the `mkblog` file, add
the shebang at the top, followed by the conditional statement shown above. Then
define the following `main()` function right above the conditional statement:

```python [mkblog]
def main() -> None:
    '''Entrypoint of the script.'''
    print("Hello World!")
```

With the contents added to the file, its contents should look similar to this
and it'll be your Python script from now on.

```python [mkblog]
#!/usr/bin/env python3

def main() -> None:
    '''Entrypoint of the script.'''
    print("Hello World!")


if __name__ == "__main__":
    main()
```

Invoking the script in an interactive shell like this - ./mkblog will output the
following message:

```shell
./mkblog
# Output: "Hello World!"
```

If you can reproduce this behavior, your script is working as expected.

Now let's learn about the `sys.argv` variable. Python uses this low-level API to
provide a high-level interface for building typical CLI applications. According
to the Python documentation, `sys.argv` is a list of command-line arguments
passed to the script. The first element, `sys.argv[0]`, is the script name:

> _The list of command line arguments passed to a Python script. `argv[0]` is
> the script name (it is operating system dependent whether this is a full
> pathname or not)._

In other words, if we executed the script like this:

```shell
./mkblog "Hello World"
```

You can access the arguments passed to the script and do something with them! To
test it out, let's edit our script with the following changes:

```python [mkblog]
# ... truncated contents of the file

import sys

def main() -> None:
    '''Entrypoint of the script.'''
    print(sys.argv[1])

# ... truncated contents of the file
```

Executing the script now produces the following output:

```shell
./mkblog "Hello World"
# Output: "Hello World"
```

If you've used **any** CLI applications before, you're familiar with their
positional arguments and options/flags that change behavior and output. Scaling
up an application with many arguments and flags using only `sys.argv` becomes
extremely verbose and error-prone.

The `argparse` module solves this problem by providing useful abstractions for
complex argument parsing. Beyond parsing, the module offers a helpful
out-of-the-box experience for building CLI applications with minimal
configuration.

In the next section we'll lay the foundations of our CLI application (or rather
the script!) and then improve it with additional feature updates.

## Setting up the Interface

Almost all CLI applications will provide a `--help` flag to print the tool's
usage guide and documentation. Fortunately for us, the `argparse` module
provides the interface to enable this functionality without any extra code
written for it! So before we start using the module, lets check out the
difference in functionality without and then with using the module.

If you ran the script as-is right now and passed a `--help` flag to it, you'll
see the following output:

```shell
./mkblog --help
# Output: "--help"
```

Not the expected behaviour, right?

An ideal CLI application should instead provide a helpful usage guide as an
output. So let's implement that functionality as a baseline to check whether our
script is in a working condition:

```python [mkblog]
#!/usr/bin/env python3

from argparse import ArgumentParser, Namespace


def parse_args() -> Namespace:
    """Parse and return the arguments/options of the script."""
    parser = ArgumentParser(description="generate a blog template")
    return parser.parse_args()


def main() -> None:
    """Entrypoint of the script."""
    parse_args()


if __name__ == "__main__":
    main()
```

Invoking the script now with the `--help` flag immediately provides some helpful
usage guide as shown below:

```shell
./mkblog --help
# Output:
# usage: mkblog [-h]
#
# generate a blog template.
#
# options:
#  -h, --help            show this help message and exit
```

Our script now resembles a typical CLI application with the addition of **ONLY**
a few lines of code! Without the `argparse` module we would have had to resort
to some complex and manual argument parsing using the `sys.argv` variable. The
help message, the formatting of the command output was all internally processed
and handled by the module itself and that is quite nifty isn't it?

Moving on, our application now has an help guide so let's now try adding some
positional arguments and few more flags to manipulate the data passed as
arguments to our app.

We can add the positional arguments and optional flags using the
`argparse.ArgumentParser.add_argument()` method (see
[the docs](https://docs.python.org/3/library/argparse.html#argparse.ArgumentParser.add_argument)
to learn more). So, to implement a `title` , the optional `--draft` and
`--output` flags, we can update our source code with these changes:

```python [mkblog]
#!/usr/bin/env python3

from argparse import ArgumentParser, Namespace


def parse_args() -> Namespace:
    """Parse and return the arguments/options of the script."""
    parser = ArgumentParser(description="generate a blog template")

    blog_dir = pathlib.Path().home() / "blogposts"

    parser.add_agument("title", type=str, help="the title of the blog post")
    parser.add_argument(
        "-d",
		    "--draft",
		    action="store_true",
		    help="create a draft blogpost, defaults to 'True'"
		)
		parser.add_argument(
		    "-o",
		    "--output",
		    type=pathlib.Path,
		    default=blog_dir
		    help="create a draft blogpost, defaults to 'True'"
		)

    return parser.parse_args()


def main() -> None:
    """Entrypoint of the script."""
    parse_args()


if __name__ == "__main__":
    main()
```

The changes introduced to our application is now quite verbose but we'll attempt
to understand the code soon enough. Before that, try invoking the script's
`--help` flag now and you will see an updated output message:

```shell
./mkblog --help
# Output:
# usage: mkblog [-h]
#
# generate a blog template.
#
# positional arguments:
# title                  the title of the blog post
#
# options:
#  -h, --help            show this help message and exit
#  -d, --draft           create a draft blog post, defaults to 'True'
#  -o, --output OUTPUT   directory to save the Markdown file at, defaults to
#                        "/home/johndoe/blogposts"
```

Again we clearly now have a positional argument (`title`), the optional
(`-draft`) and `--output` flags thanks to the `argparse` module!

In the current version of our code, our `parse_args()` function basically
creates a "parser" from the `ArgumentParser()` object and returns a `Namespace`
object containing all the arguments and flags of our application. Internally,
our `parser` object containing the `add_argument()` and the `parse_args()`
methods among a few which is not discussed in this article. The `add_argument()`
method is used to append the CLI arguments and flags to the `Namespace` object
and the `parse_args()` method creates the `Namespace` object.

The `add_argument()` method also accepts a bunch of parameters some of which are
used in our use case. To start with, the `store_true` parameter configures our
`--draft` flag to instruct the application to false a "truth-ey value" if
explicitly passed and vice versa. We can also provide some type-casting in the
application by passing the `type` parameter. For example, our `--output` flag
only accepts a filesystem path-like object. In addition, we also set a default
to the flag by using the `default` parameter which is set to a file-path. The
default variable is set using the `pathlib` module which itself deserves another
article of its own so I won't discuss much about it right now.

The list of supported parameters for the `add_argument()` method is larger and I
only briefly glanced over them in my article. Hence I urge you to check out the
official documentation of the `argparse` module to learn more about the
parameters to pass to the `add_argument()` method.

With the changes mentioned above, we now have a working CLI interface but it is
dumb and does nothing other than notify the user about its potential
capabilities. So in the next section of the document we'll implement the logic
to handle those functions.

## Reading the Template and Generating the Boilerplate

Our CLI application is only halfway implemented and does nothing other than
parsing the CLI arguments and exiting the execution loop successfully. You can
verify it by invoking the following command and checking whether the command
exits without any issues;

```shell
./mkblog "Hello World"
# Outputs nothing
# Verify command ran successful by checking exit code
# echo $?
```

At the beginning of the article I mentioned the purpose of the script and tasks
it is expected to fulfill but as a reminder here's what it should do:

1. Read and parse some pre-defined template content.
2. Create a Markdown file and populate it with the pre-defined template content.
3. Notify the user of the task completion.

So in this section we'll work on implementing the logic to handle those tasks
properly. To do so, we'll first define two utility functions -
`create_content()` and `write_file()` .

Our `create_content()` function will be defined as such:

```python [mkblog]
# .... truncated code

def create_content(title: str, draft: bool) -> str:
		"""Parse and generate the Markdown content to write to file."""
		status = "draft" if draft else "published"

		return f"""
		---
		title: {title}
		status: {status}
		---
		"""

# .... truncated code
```

Our `create_content()` function fulfills the simple purpose of returning an
extrapolated string of YAML front-matter which can be added to our freshly
generated Markdown file. The extrapolated string is created based on the `title`
and `draft` parameters passed to the function which is the title of the blog
post and the publication status, respectively.

The function uses "f-strings" which is a pretty powerful means of dynamically
manipulating strings in Python. This feature of the Python programming language
is quite powerful and one of my favourite feature of the language, hence I plan
to write an explicit article on it someday. Until then I strongly urge you to
check out
[the relevant docs](https://docs.python.org/3/reference/lexical_analysis.html#f-strings)
to learn more).

The `write_file()` function on the other hand is defined as such:

```python
import re

# .... truncated code

def write_file(title: str, output: pathlib.Path, draft: bool) -> pathlib.Path | None:
		"""Generate a Markdown file based on the created content."""
		if not pathlib.Path(output)is_dir():
				pathlib.Path(output).mkdir(parents=True, exists_ok=True)

		title = re.sub(r"[^a-z0-9-]", "", title.lower().replace(" ", "-"))
		filepath = output / title / ".md"

		if filepath.exists():
				print(f"[ERROR] {filepath} aleady exists, not overwriting it...")
				return None

		content = create_content(title, draft)
		filepath.write_text(content)

		return filepath

# .... truncated code
```

The function accepts the `title`, `output` and `draft` parameters which are
basically the title of the blog post, the file path to write the Markdown file
to and its publication status. These parameters are passed down to the
`create_content()` function from earlier and based on its returned data, a
Markdown file be generated at a specified location.

The function also prevents accidentally overwriting the Markdown if it already
exists and in addition creates the necessary directories (if any are required)
to store the Markdown file at. Once the function completes the execution of its
body, it returns the file path of the generated Markdown file. This output will
be used to notify the user about successful execution for better UI/UX.

## Hooking the Interface to the Logic Handlers

Our script is now nearing completion so let us refactor our application so that
it can pass the user data from the arguments/options to our user-defined
functions for logic handling.

We'll have to make the following changes to the `main()` function of our script
like this:

```python [mkblog]
def main() -> None:
		"""Entrypoint of the script."""
		args = parse_args()

		filepath = write_files(args.title, args.output, args.draft)

		if filepath:
				print(f"[INFO] Blog template is generated at {str(filepath)}")
```

These new lines of code basically instructs our application to parse the CLI
arguments and store it in the `args` variable which are then passed to the
`write_file()` function. This function in turn returns the file path to the file
(if it was successfully written!) and the path is then printed out to the STDOUT
for the user.

Putting the entire code together, the complete script should look like this:

```python [mkblog]
#!/usr/bin/env python3

from pathlib import Path
from argparse import ArgumentParser, Namespace


def create_content(title: str, draft: bool) -> str:
		"""Parse and generate the Markdown content to write to file."""
		status = "draft" if draft else "published"

		return f"""
		---
		title: {title}
		status: {status}
		---
		"""


def write_file(title: str, output: Path, draft: bool) -> Path | None:
		"""Generate a Markdown file based on the created content."""
		if not Path(output)is_dir():
				Path(output).mkdir(parents=True, exists_ok=True)

		filepath = output / title / ".md"

		if filepath.exists():
				print(f"[ERROR] {filepath} aleady exists, not overwriting it...")
				return None

		content = create_content(title, draft)
		filepath.write_text(content)

		return filepath


def parse_args() -> Namespace:
    """Parse and return the arguments/options of the script."""
    parser = ArgumentParser(description="generate a blog template")

    blog_dir = Path().home() / "blogposts"

    parser.add_agument("title", type=str, help="the title of the blog post")
    parser.add_argument(
		    "-d",
		    "--draft",
		    action="store_true",
		    help="create a draft blogpost, defaults to 'True'"
		)
		parser.add_argument(
		    "-o",
		    "--output",
		    type=Path,
		    default=blog_dir
		    help="create a draft blogpost, defaults to 'True'"
		)

    return parser.parse_args()


def main() -> None:
		"""Entrypoint of the script."""
		args = parse_args()

		filepath = write_files(args.title, args.output, args.draft)

		if filepath:
				print(f"[INFO] Blog template is generated at {str(filepath)}")


if __name__ == "__main__":
    main()
```

If now invoke the script, a template Markdown file will be created at this
location - `/home/<USERNAME>/blogposts` (by default);

```shell
./mkblog "Hello World"
# Outputs:
# [INFO]: Blog template is generated at /home/johndoe/blogposts/hello-world.md
```

The script's behaviour can be further manipulated by passing the optional flags
to it. For example, the output file path can be changed like this:

```shell
./mkblog "Hello World" --output "./my-blog/blogs"
# Outputs:
# [INFO]: Blog template is generated at /home/johndoe/my-blog/blogs/hello-world.md
```

## Key Takeaways and Suggestions

If it wasn't obvious already, the builtin `argparse` module is extremely
versatile and usable for everyday usage. At my workplace, we've completely
ditched usage of any 3rd-party packages in favour of the standard library
provided facilities!

While the article does not fully describe the entire feature set of the module,
my intention of sharing a write-up on the topic was to shed some insight into
using the Python standard library to build CLI applications. Of course there is
a lot of room to improve the script even further but that is an assignment I
will leave for you to work on your own time and effort.

Regardless some ideas I can share to improve the functioning and implementation
of the tool are:

1. Improved logging statements (both to file and STDOUT) using the `logger`
   module from the Python standard library. Another awesome piece of work from
   the standard library which requires its own write-up.
2. Parse and read templates from disk (or a remote location) and extrapolate the
   data based on user input. The implementation can also be refactored to use
   OOP patterns to store dynamic logic which should also improve the legibility
   and functionality of the implementation.
3. Utilise the `.add_mutually_exclusive_group()` method to implement arguments
   and options which cannot be used together. For example, a `--status` and the
   existing `--draft` flags which does not make sense to be included together.

Considering the module's feature set and capabilities, how you implement new
functions for an application is only constrained by your creative thinking!
