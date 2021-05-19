# pass2csv

Source is available [at GitHub](https://github.com/reinefjord/pass2csv).

You can install it directly from PyPI with pip:

    python3 -m pip install --user pass2csv


## Usage

```
$ pass2csv --help
usage: pass2csv [-h] [-b path] [-g executable] [-a] [--encoding encoding] [-o file]
                [-e pattern [pattern ...]] [-f name pattern] [-l name pattern]
                store_path

positional arguments:
  store_path            path to the password-store to export

optional arguments:
  -h, --help            show this help message and exit
  -b path, --base path  path to use as base for grouping passwords
  -g executable, --gpg executable
                        path to the gpg binary you wish to use (default 'gpg')
  -a, --use-agent       ask gpg to use its auth agent
  --encoding encoding   text encoding to use when reading gpg output (default
                        'utf-8')
  -o file, --outfile file
                        file to write exported data to (default stdin)
  -e pattern [pattern ...], --exclude pattern [pattern ...]
                        regexps for lines which should not be exported
  -f name pattern, --get-field name pattern
                        a name and a regexp, the part of the line matching the
                        regexp will be removed and the remaining line will be added
                        to a field with the chosen name. only one match per
                        password, matching stops after the first match
  -l name pattern, --get-line name pattern
                        a name and a regexp for which all lines that match are
                        included in a field with the chosen name
```


### Format

The output format is

    Group(/),Title,Password,[custom fields...],Notes

You may add custom fields with `--get-field` or `--get-line`. You supply
a *name* for the field and a regexp *pattern*. The field name is used for
the header of the output CSV and to group multiple patterns for the same
field; you may specify multiple patterns for the same field by
specifying `--get-field` or`--get-line` multiple times with the same
name. Regexps are case-insensitive.


### Examples

* Password entry (`~/.password-store/sites/example/login.gpg`):

```
password123
---
username: user_name
email user@example.com
url:example.com
Some note
```

* Command

```
pass2csv ~/.password-store \
  --exclude '^---$' \
  --get-field Username '(username|email):?' \
  --get-field URL 'url:?'
```

* Output

```
Group(/),Title,Password,URL,Username,Notes
sites/example,login,password123,example.com,user_name,"email user@example.com\nSome note"
```


### Grouping

The group is relative to the path, or the `--base` if given.
Given the password `~/.password-store/site/login/password.gpg`:

    $ pass2csv ~/.password-store/site
        # Password will have group "login"

    $ pass2csv ~/.password-store/site --base:~/.password-store
        # Password will have group "site/login"


## Development

Create a virtual environment:

    python3 -m venv venv

Activate the environment:

    . venv/bin/activate

Now you may either use `pip` directly to install the dependencies, or
you can install `pip-tools`. The latter is recommended.


### pip

    pip install -r requirements.txt


### pip-tools

[pip-tools](https://github.com/jazzband/pip-tools) can keep your virtual
environment in sync with the `requirements.txt` file, as well as
compiling a new `requirements.txt` when adding/removing a dependency in
`requirements.in`.

It is recommended that pip-tools is installed within the virtual
environment.

    pip install pip-tools
    pip-compile  # only necessary when adding/removing a dependency
    pip-sync
