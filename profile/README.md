## A protocol for command line completion

Think of LSP but for command line.

Command line completion is popular in the Unix world as it helps inputting commands quickly, prevents typos and makes the shell more user-friendly.

## The problem

Implementing command-specific completion like `git com<tab>mit` takes time and effort.

Completion scripts use different syntax across Bash, Zsh and Fish.
Sometimes they are out of date or are missing options.

## A proposal

Shellcomp is a simple protocol for CLI applications to teach shells how to do completion.

Completion is implemented in the command about to be run:
The shell run the command with a specific `--shellcomp '<current_arguments>'` option.
The command responds a JSON structure that the shell will parse to perform completion and display help messages.

## The benefits

* It's shell-independent
* Shells will suggest alternative commands and provide contextual help
* Discoverable! The completion mechanism can be used to autogenerate:
 * documentation
 * manpages
 * simple GUIs!
 * even generate tests

## The JSON format

The JSON document is to be printed to stdout; All fields are optional:

v
  version.
completions
  a list of valid completion values with an optional help message.
alt
  alternative completions: other commands that the user might want to use.
help
  contextual help message.

An example response for `git com<TAB>`:

```json
    {
        "v": 1,
        "completions": [
            ["commit", "record changes to repository"]
        ]
        "alt": [
            ["config", "get and set repository or global options"],
        ]
        "help": "Add `-a` to commit all files that have been modified or deleted",
    }
```

An example for `git remote <TAB>` (showing only two arguments for brevity):

```json
    {
        "v": 1,
        "completions": [
            ["add", "Add a remote"],
            ["rename", "Rename a remote"],
        ]
        "help": "Manage set of tracked repositories",
    }
```

### Fields usage

* `v`: format version. Should be 1.
* `completions`: a list of valid completion values in format ["completion string", "help string"]. The help string can be empty. An empty list means that no more arguments are expected. An item with an empty completion string means that non-completable user input is expected (e.g. touch <new_filename>).
* `alt`:  alternative completions. Same structure as `completions`; it contains suggestions about other commands that the user might want to use instead.
* `help`: contextual help message. It can contain multiple lines.
* `errpos`:  if the user-supplied string contains a syntax error, this is the position of the first error, counting from 0 from the left.
* `files`:  a list of filenames, in case the current argument is meant to be an existing file. Using a different field other than "completions" allow shells to display the filenames in different colors

### How to implement it

In the long term, popular shells like Bash, Zsh, Fish could implement the standard internally.

In the short term, a simple wrapper could run the application on behalf of the shell and perform "traditional" completion. 

Applications could implement completion autonomously or through helper libraries
like `DocOpt <http://docopt.org/>`_ or `Click <http://click.pocoo.org/5/>`_

## Contributing

Please join the project!

* Discuss it here: https://github.com/shellcomp/.github/discussions
* Suggest changes to this document: https://github.com/shellcomp/.github/edit/main/profile/README.md
* Contribute shellcomp libraries
* Implement shellcomp in Bash, Zsh & co
* Implement shellcomp in your CLI tool!
