# jot

![](https://img.shields.io/github/v/tag/agorf/jot?label=version&sort=semver)

**jot** is a minimal, command-line note-taking program written in [Bash][].

[Bash]: https://en.wikipedia.org/wiki/Bash_(Unix_shell)

It integrates seamlessly with your text editor and terminal and supports the minimum number of features, getting out of your way.

## Design goals

- [Small source size][source] (~250 [LOC][]: `grep -v '^#' jot | grep -v '^$' | wc -l`)
- Easy [installation](#installation)
- Plain text [note files](#notes) edited with a [text editor](#jot_editor)
- Few [commands](#commands) with sensible defaults and aliases for less typing
- Seamless integration with external programs through [custom commands](#custom-commands)
- Extensibility and automation with custom [command hooks](#command-hooks)

[source]: https://raw.githubusercontent.com/agorf/jot/master/jot
[LOC]: https://en.wikipedia.org/wiki/Source_lines_of_code

Check out some [usage examples](#usage-examples).

## Installation

```shell
wget -qO - 'https://raw.githubusercontent.com/agorf/jot/master/jot' | sudo tee /usr/local/bin/jot >/dev/null
```

If [wget][] is not available in your system, you can use [curl][]:

[wget]: https://www.gnu.org/software/wget/
[curl]: https://curl.haxx.se/

```shell
curl -sS 'https://raw.githubusercontent.com/agorf/jot/master/jot' | sudo tee /usr/local/bin/jot >/dev/null
```

Finally, make jot executable:

```shell
sudo chmod +x /usr/local/bin/jot
```

## Notes

Notes are plain text files:

- With a `YYYY-MM-DD` filename
- A [`$JOT_EXT`](#jot_ext) extension
- Kept under [`$JOT_HOME`](#jot_home)

## Dates

[Commands](#commands) accept _one or more_ dates and map them to note file names.

For example, if:

- Today is the 15th of April, 2020
- [`$JOT_HOME`](#jot_home) is `/home/agorf/jot/`
- [`$JOT_EXT`](#jot_ext) is `txt`

Then `today` is mapped to `/home/agorf/jot/2020-04-15.txt`

The following date aliases are supported:

### `today`

Alias: `td`

Self-explanatory.

This is the default date if you don't provide one.

### `yesterday`

Alias: `yd`

Self-explanatory.

### `tomorrow`

Alias: `tm`

Self-explanatory.

### `prev`

Previous day with notes.

### `pwd`

Previous working day.

It returns previous Friday's date when ran on Saturday, Sunday or Monday.

### `nwd`

Next working day.

It returns next Monday's date when ran on Friday, Saturday or Sunday.

### `all`

Targets all dates.

This makes it possible to execute custom commands targeting all note files.

For example, the following lists all note files:

```shell
jot ls -l -- all
```

### Custom dates

Any date not matching the above is passed as the value of the `--date` option to the system's `date` command (`man 1 date`).

Example: `2020-04-15` for the 15th of April, 2020

Example: `"3 days ago"`

Example: `"1 day"` (ahead)

### `dir`

This is not a date and does not map to note files. It can be used to issue commands against the `$JOT_HOME` directory.

For example, the following lists all `$JOT_HOME` directory contents:

```shell
jot ls -l -- dir
```

Contrast this with the following which lists all note files:

```shell
jot ls -l -- all
```

And this which lists only today's note file:

```shell
jot ls -l
```

## Commands

### `edit`

Alias: `e`

Edit note file with [`$JOT_EDITOR`](#jot_editor)

This is the default if you don't provide one.

Examples:

```shell
jot edit today
jot e td
jot e
jot e yd
jot e yd td
jot e td tm
jot e all
```

### `copy`

Alias: `c`

Copy note file contents to the clipboard with [`$JOT_COPY`](#jot_copy)

Lines beginning with `#` are considered comments and are not copied. This can be used to keep note lines private when copy-pasting.

Examples:

```shell
jot copy today
jot c td
jot c
jot c yd
```

### `list`

Alias: `l`

List all note files in ascending date order (most recent, last)

For each file, the following is displayed:

- Optional `>` marker to signify today's note file
- Date
- Number of lines
- Path

Example output (truncated):

```
  Fri, 24 Apr 2020 ( 7) /home/agorf/jot/2020-04-24.txt
  Mon, 27 Apr 2020 ( 3) /home/agorf/jot/2020-04-27.txt
  Tue, 28 Apr 2020 ( 8) /home/agorf/jot/2020-04-28.txt
  Wed, 29 Apr 2020 ( 8) /home/agorf/jot/2020-04-29.txt
  Thu, 30 Apr 2020 ( 8) /home/agorf/jot/2020-04-30.txt
> Mon, 04 May 2020 ( 7) /home/agorf/jot/2020-05-04.txt
  Tue, 05 May 2020 ( 4) /home/agorf/jot/2020-05-05.txt
```

### `help`

Aliases: `-h`, `--help`

Print usage help text.

### `version`

Show jot version.

### `update`

Update jot to latest version.

Needs `wget` or `curl` to be installed.

### Custom commands

If the command is not one of the above, jot will execute it, passing to it as arguments any dates after `--` mapped to note file names.

This makes it possible to call arbitrary commands with note files!

For example, to delete yesterday's and tomorrow's note files:

```shell
jot rm -- yd tm
```

To ask for confirmation before deleting:

```shell
jot rm -i -- yd tm
```

Check out the [usage examples](#usage-examples).

## Command hooks

The [edit](#edit) and [custom commands](#custom-commands) support "pre" and "post" hooks with `$JOT_HOOKS/pre` and `$JOT_HOOKS/post` respectively. Hooks are custom scripts marked as executable (`chmod +x`) that are executed before and/or after the command.

For example, the execution order for the [edit command](#edit) is:

- `$JOT_HOOKS/pre`
- `edit` command
- `$JOT_HOOKS/post`

The following `post` hook adds changes to a [Git][] repository and pushes them to the remote, so that note files are backed up and synchronized:

[Git]: https://git-scm.com/

```shell
# Place this under $JOT_HOOKS/post and make it executable with chmod +x

[[ -z "$(git status -s)" ]] && exit

git add --all
git commit -m "$(date)"
git push
```

## Environment variables

The following environment variables are supported:

### `JOT_HOME`

Default: `$HOME/jot`

Where note files are stored.

### `JOT_HOOKS`

Default: `$JOT_HOME/hooks`

Where [hook scripts](#command-hooks) are stored.

### `JOT_EDITOR`

Default: `$EDITOR`

Text editor to edit note files with.

### `JOT_EDITOR_OPTS`

Default: (empty)

Options passed to the [`$JOT_EDITOR`](#jot_editor) command.

Here's what I use for [Vim][]:

[Vim]: https://www.vim.org/

```
export EDITOR=vim
export JOT_EDITOR_OPTS='"+normal G" -O'
```

This ensures each file is opened in a vertical split window and the cursor is placed at the end of the file.

### `JOT_COPY`

Default: (none)

Note file contents will be passed to the standard input of this command to be copied to the clipboard.

jot will auto-detect the existence of the following X selection manipulation programs (in this order):

- [xclip](https://github.com/astrand/xclip)
- [xsel](http://www.vergenet.net/~conrad/software/xsel/)
- [xcut](http://xcut.sourceforge.net/)
- [sselp](http://tools.suckless.org/x/sselp/)

### `JOT_DATE_FMT`

Default: `%a, %d %b %Y`

Example: Wed, 15 Apr 2020

How note file dates are displayed with the [`list`](#l-list) command.

### `JOT_EXT`

Default: `txt`

File extension used for note files.

## Usage examples

Edit today's notes:

```shell
jot edit today
```

`edit` is aliased as `e` and `today` as `td`, so the following is equivalent:

```shell
jot e td
```

Since `edit` and `today` are the defaults, you can simply execute `jot` instead:

```shell
jot
```

Edit yesterday's (`yd` or `yesterday`) and today's notes:

```shell
jot e yd td
```

Edit all notes:

```shell
jot e all
```

Copy today's notes to the clipboard:

```shell
jot copy today
```

Shortened:

```shell
jot c td
```

Since `today` is the default:

```shell
jot c
```

List all notes:

```shell
jot list
```

Shortened:

```shell
jot l
```

Remove tomorrow's notes, asking for confirmation:

```shell
jot rm -iv -- tm
```

Open today's notes with `less` pager:

```shell
jot less -- td
```

Since today is the default:

```shell
jot less
```

Show file information for today's notes:

```shell
jot ls -lh
```

Concatenate yesterday's and today's notes:

```shell
jot cat -- yd td
```

Search all notes for `@agorf`, colorizing matches:

```shell
jot grep --color @agorf -- all
```

Display number of lines for all notes:

```shell
jot wc -l -- all
```

Display [Git][] directory status in `$JOT_HOME`:

```shell
jot git status -- dir
```

## Acknowledgements

I was inspired to start working on jot from [iridakos][] who wrote [stup][].

[iridakos]: https://iridakos.com/
[stup]: https://github.com/iridakos/stup

## License

[The MIT License][]

[The MIT License]: https://github.com/agorf/jot/blob/master/LICENSE.txt

## Author

Angelos Orfanakos, <https://angelos.dev>
