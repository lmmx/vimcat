# vimcat

> `cat` with `vim` (a.k.a. `view`) syntax highlighting

Free [syntax highlighting](http://vimdoc.sourceforge.net/htmldoc/syntax.html#syntax) from `vim`,
often better than GNU `source-highlight` (e.g. colours escaped characters distinctly within string
literals).

This minimal repo contains only the LICENSE, README, and a modified `vimcat`

## Modifications

- Removed the carriage return (`\r`) from the start of each line of the buffer
  - I'm unclear why this was introduced, as this is a Mac newline feature and the script seems to
    check if the system code is `darwin`). Remove the `sed` call if you're a Mac user.
- `tail`s the output to omit the first line, with the file name (to match the behaviour
  of `cat`, which its name suggests this program is supposed to emulate)
- **(Under development)** Arbitrary syntax highlighting

## TODO

- Add a flag to set syntax highlighting since file type won't be available from a file extension when
  used over a pipe
  - The core command itself calls vim with `-c`, and syntax highlighting can be set with `-c`
  - E.g. `:set syntax=html` 'internally' ([within vim](http://vimdoc.sourceforge.net/htmldoc/options.html#'syntax'))
    becomes `-c "set syntax=html"` as a command to `vim`.
  - N.B. can fail to complete on large files, and cannot be rescued with `^C`, `^D` or `^\`
    termination signals.
- Install signal handler `sigaction` to catch termination signals (see
  [here](https://stackoverflow.com/a/7376297/2668831) for general notes more geared to C, and
  [here](https://stackoverflow.com/a/22857240/2668831) for bash script related implementation notes)

## Omissions

The makefiles and RedHat Linux-related files have been omitted, see links to previous repo versions
below if you need these.

## Example usage

- `vimcat myfile.py`
- `vimcat myfile.py | less -R`

It even works with commands like `diff`, e.g.

- `diff <(vimcat file1) <(vimcat file2)`

## Previous owners

This script has a few previous owners, whose copyright lines are retained as per license
conditions (see comment header of [vimcat](vimcat)):

- 2020: "The FrauBSD Project" (Twitter: [freebsdfrau](https://twitter.com/freebsdfrau))
  - Source repo: [FrauBSD/vimcat](https://github.com/FrauBSD/vimcat)
- 2017: "Devin Teske" (FreeBSD)
- 2008: "Matthew J. Wozniski" (Drexel University)

I tried the packaged version (available [here](https://github.com/ofavre/vimcat)) and found it was
too slow to be practically usable. The script here seems to take no longer than `vim` startup time,
which is fractionally longer than plain `cat` but worthwhile (especially within scripts which
are already expected to run in non-negligible time).

## Related

### ToHTML

To inspect this same colour-coded text there is also the `:ToHTML` command (shipped with `vim`),
which dumps a HTML file, and can be run 'headless'.

```sh
vim -E -c "TOhtml" -c "w" -c "qa!" -- test.c >/dev/null
```

See the Q&A and links
[here](https://superuser.com/questions/314833/how-can-i-run-a-headless-gvim-to-get-syntax-highlighted-code-converted-to-html)
for further information and vimdoc [documentation](http://vimdoc.sourceforge.net/htmldoc/syntax.html).

E.g. if you download the binary of [`pup`](https://github.com/EricChiang/pup) you can get a JSON
version and then pipe it to `jq` to extract the tags only, and use this sequence of tags in
combination with your ANSI code sequences (which will only indicate changes) to get an annotated
version of the ANSI codes (if you'd want that for some reason).

### vipe

Part of `moreutils` (available on `apt`), `vipe` allows you to run an editor within a pipe.
To do so requires you to set the `EDITOR` environment variable in your `bashrc` exports
(`export EDITOR=vim`).
