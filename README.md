# jot

**jot** is a minimal, general-purpose, command-line note-taking program written in [Bash][].

[Bash]: https://en.wikipedia.org/wiki/Bash_(Unix_shell)

It integrates seamlessly with your text editor and terminal and supports the minimum number of features, getting out of your way.

## Design goals

- [Small size][source] (~200 [LOC][]: `grep -v '^#' jot | grep -v '^$' | wc -l`)
- Easy [installation](#installation)
- Sane defaults
- [Notes](#notes) as plain text files
- Use from a terminal
- Few (3) [commands](#commands)
- Minimal keystrokes
- Use a [text editor](#jot_editor) to edit note files
- Combine with other programs with [custom commands](#custom-commands)
- Extend with [command hooks](#command-hooks)

[LOC]: https://en.wikipedia.org/wiki/Source_lines_of_code
[source]: https://raw.githubusercontent.com/agorf/jot/master/jot

You can see some [usage examples](#usage-examples).

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
- A `$JOT_EXT` extension
- Kept under `$JOT_HOME`

See [Environment variables](#environment-variables) for more.

## Dates

jot commands accept dates and map them to note file names.

For example, if:

- Today is the 15th of April, 2020
- `$JOT_HOME` is `/home/alice/jot/`
- `$JOT_EXT` is `txt`

Then `today` is mapped to `/home/alice/jot/2020-04-15.txt`

The following date aliases are supported:

### `td` (`today`)

Self-explanatory.

This is the default date if you don't provide one.

### `yd` (`yesterday`)

Self-explanatory.

### `tm` (`tomorrow`)

Self-explanatory.

### `prev` (`pwd`)

Stands for "**p**revious **w**orking **d**ay". It returns last Friday's date when ran on Saturday, Sunday or Monday.

### `next` (`nwd`)

Stands for "**n**ext **w**orking **d**ay". It returns next Monday's date when ran on Friday, Saturday or Sunday.

### `all`

Targets all dates.

This makes it possible to execute custom commands targeting all note files (see [Usage examples](#usage-examples))

### Absolute dates

Of the form `YYYY-MM-DD`

Example: `2020-04-15` for the 15th of April, 2020

### Relative dates

Example: `"3 days ago"`

Example: `"1 day"` (ahead)

These should be quoted since they contain spaces.

### Custom dates

Any date not matching the above is passed as the value of the `--date` option to the system's `date` command (`man 1 date`). That's actually how relative dates work.

## Commands

### `e` (`edit`)

Hook scripts: `$JOT_HOOKS/pre-edit`, `$JOT_HOOKS/post-edit`

Edit note file with `$JOT_EDITOR` (see [Environment variables](#environment-variables))

This is the default if you don't provide one.

### `c` (`copy`)

Hook scripts: `$JOT_HOOKS/pre-copy`, `$JOT_HOOKS/post-copy`

Copy note file contents to the clipboard.

Lines beginning with `#` are considered comments and are not copied. This can be used to keep note lines private when copy-pasting.

### `l` (`list`)

Hook scripts: `$JOT_HOOKS/pre-list`, `$JOT_HOOKS/post-list`

List all note files in ascending date order (most recent, last)

For each file, the following is displayed:

- Optional `>` marker to signify today's note file
- Date
- Number of lines
- Path

### `help` (`-h`, `--help`)

Print usage help text.

### `version`

Show jot version.

### Custom commands

Hook scripts: `$JOT_HOOKS/pre-execute`, `$JOT_HOOKS/post-execute`

If the command is not one of the above, jot will execute it, passing to it any dates after `--` as arguments mapped to note files.

This makes it possible to call arbitrary commands/programs with note files, resulting in endless possibilities!

For example, to delete yesterday's and tomorrow's note files, simply issue:

```shell
jot rm -vi -- yd tm
```

See [Usage examples](#usage-examples) for more.

## Command hooks

All commands support "pre" and "post" hooks, i.e. arbitrary scripts that are executed before and/or after each jot command (see [Commands](#commands))

One possible use of hooks is to automatically add changes to a [Git][] repository and push them to a remote (e.g. for backup and synchronization)

[Git]: https://git-scm.com/

Another idea is to post a message to a remote service, e.g. [Slack][].

[Slack]: https://slack.com/

Please note that **hook scripts must be executable** (`chmod +x`) in order to work.

## Environment variables

jot can be configured through the following environment variables:

### `JOT_HOME`

Default: `$HOME/jot`

Where note files are stored.

### `JOT_HOOKS`

Default: `$JOT_HOME/hooks`

Where hook scripts are stored (see [Command hooks](#command-hooks))

### `JOT_EDITOR`

Default: `$EDITOR`

The text editor to edit note files with.

### `JOT_EDITOR_OPTS`

Default: (empty)

Options passed to the `$JOT_EDITOR` command.

Here's what I use for [Vim][]:

[Vim]: https://www.vim.org/

```
export EDITOR=vim
export JOT_EDITOR_OPTS='"+normal G" -O'
```

This ensures each file is opened in a vertical split window and the cursor is placed at the end of the file.

### `JOT_COPY`

Default: [`xclip`][xclip]

[xclip]: https://github.com/astrand/xclip

Note file contents will be passed to the standard input of this command to copy them to the clipboard.

Other popular alternatives: [xsel][], [sselp][], [xcut][]

[xsel]: http://www.vergenet.net/~conrad/software/xsel/
[sselp]: http://tools.suckless.org/x/sselp/
[xcut]: http://xcut.sourceforge.net/

### `JOT_DATE_FMT`

Default: `%a, %d %b %Y`

Example: Wed, 15 Apr 2020

How note file friendly dates are displayed with the `list` command (see [Commands](#commands))

### `JOT_EXT`

Default: `txt`

The file extension used for note files.

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

Copy today's notes:

```shell
jot copy today
```

With aliases:

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

With aliases:

```shell
jot l
```

Remove tomorrow's note, asking for confirmation:

```shell
jot rm -iv -- tm
```

Open today's notes with a pager program:

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

Search all notes for `Jane`, colorizing matches:

```shell
jot grep --color Jane -- all
```

Display number of lines for all notes:

```shell
jot wc -l -- all
```

## Credits

I got the idea to start working on jot from [iridakos][] who wrote [stup][].

[iridakos]: https://iridakos.com/
[stup]: https://github.com/iridakos/stup

## License

jot is licensed under the terms of the [MIT license][].

[MIT license]: https://github.com/agorf/jot/blob/master/LICENSE.txt

## Author

Angelos Orfanakos, <https://angelos.dev>
