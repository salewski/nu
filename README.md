This is the README.md file for the `'nu'` project.

# Overview

The 'nu' project provides the nu(1) program that emits content with numbered
lines on your console in a style modeled after that produced by the `'set nu'`
command in the [vi(1) editor][VI_WIKIPEDIA] and many of its clones.

This project is just being created (2016-12-18); proper documentation does not
yet exist but hopefully will soon. For now, just copy the 'nu' script onto
your path and invoke it like this:

``` bash
$ nu some-file some-other-file
```
or
``` bash
$ cat some-file some-other-file | nu
```

The program numbers lines from any specified files (or from `stdin`, if no
files are provided). The output is emitted on `stdout`.

To give an idea of how this is used, here's an example of `'nu'` being run on
its own source file:


``` bash
$ nu path/to/bin/nu | tail -n 30
363 t_last_line_number_digit_count=${#t_last_line_number}
364 
365 if test $t_last_line_number_digit_count -ge $max_field_width; then
366 
367     # No reason to do anything else; our processing here would not be adding
368     # anything useful to the default produced by nl(1), so we'll just show it
369     # directly.
370     #
371     # Note that if we're falling through here, the output is still predictable
372     # (should not break downstream tools attempting to parse it) but will not
373     # be nicely formatted for display beyond the billionth line.
374     #
375     "${CAT_PROG}" "${TDIR_SPOOL_FPATH}"
376     if test $? -ne 0; then
377         printf "${PROG} (error): was unable to read spooled output from temporary file \"%s\"; bailing out\n" "${TDIR_SPOOL_FPATH}" 1>&2
378         exit 1
379     fi
380 
381     exit 0
382 fi
383 
384 t_slice_off_cnt=$(( $max_field_width - $t_last_line_number_digit_count ))
385 "${CAT_PROG}" "${TDIR_SPOOL_FPATH}" \
386 | "${SED_PROG}" -e 's#^[[:space:]]\{0,'"${t_slice_off_cnt}"'\}##'
387 if test $? -ne 0; then
388     printf "${PROG} (error): was failure while processing spooled output from temporary file \"%s\"; bailing out\n" "${TDIR_SPOOL_FPATH}" 1>&2
389     exit 1
390 fi
391 
392 exit 0
```

The line numbers are prepended unconditionally to every line of input, and are
emitted right-aligned in a field just wide enough for the largest line number
to be displayed. Another example illustrates the point, showing the numbering
where it crosses from lines with 2-digit line numbers to lines with 3-digit
line numbers:


``` bash
$ nu path/to/bin/nu | grep -C 15 '^[[:space:]]*100\>'
 85 # particular tool by setting an environment variable named after the tool
 86 # (with hyphen chars changed to underscores).
 87 
 88 #declare -r AWK_PROG="${AWK:-@AWK@}"  # 'awk' command found at configure time 
 89 declare -r AWK_PROG="${AWK:-awk}"  # 'awk' command, first found on $PATH
 90 
 91 # declare -r CAT_PROG="${CAT:-@CAT@}"
 92 declare -r CAT_PROG="${CAT:-cat}"
 93 
 94 #declare -r MKTEMP_PROG="${MKTEMP:-@MKTEMP@}"  # 'mktemp' command found at configure time 
 95 declare -r MKTEMP_PROG="${MKTEMP:-mktemp}"  # 'mktemp' command, first found on $PATH
 96 
 97 #declare -r NL_PROG="${NL:-@NL@}"  # GNU 'nl' command (from GNU coreutils) found at configure time 
 98 declare -r NL_PROG="${NL:-nl}"  # GNU 'nl' command (from GNU coreutils), first found on $PATH
 99 
100 #declare -r RM_PROG="${RM:-@RM@}"  # 'rm' command found at configure time 
101 declare -r RM_PROG="${RM:-rm}"  # 'rm' command, first found on $PATH
102 
103 #declare -r RMDIR_PROG="${RMDIR:-@RMDIR@}"  # 'rmdir' command found at configure time 
104 declare -r RMDIR_PROG="${RMDIR:-rmdir}"  # 'rmdir' command, first found on $PATH
105 
106 #declare -r SED_PROG="${SED:-@SED@}"  # 'sed' command found at configure time 
107 declare -r SED_PROG="${SED:-sed}"  # 'sed' command, first found on $PATH
108 
109 #declare -r TAIL_PROG="${TAIL:-@TAIL@}"  # 'tail' command found at configure time 
110 declare -r TAIL_PROG="${TAIL:-tail}"  # 'tail' command, first found on $PATH
111 
112 
113 BE_VERBOSE=false
114 
115 
```

See the [BUGS] file for information on reporting bugs.


## FAQ

### Q: Why do we need a new tool to number lines? Can't the same thing be achieved with [nl(1)][NL_WIKIPEDIA]?

**A:** The `'nu'` tool actually uses the `nl(1)` internally to obtain the line
numbering. The `'nu'` tool was motivated by the author spending time
repeatedly futzing with long command lines to obtain the output in the format
produced by '`nu`'. The `'nu'` program is simply a wrapper script optimized
for the author's common case scenario.

### Q: Why is the program named 'nu'?

**A:** It is a mnemonic for the command `':set nu'` used to turn on line numbering in the [vi(1) editor][VI_WIKIPEDIA] and many of its clones.

There is also precedent for this naming approach in related tools. For
example, amongst the [many ways][EMACS_LINENUM] to display line numbers within
[emacs][EMACS] there is the similarly named [setnu-mode][SETNU_EL] (and its
cousin [setnu+-mode][SETNU_PLUS_EL]), names also derived from the same vi
command.


## Copyright (Copyleft)

You can see the version number and copyright (copyleft) information via:
``` bash
$ nu --version
nu 0.0.1

Copyright (C) 2016 Alan D. Salewski <salewski@att.net>
License GPLv2+: GNU GPL version 2 or later <http://gnu.org/licenses/gpl.html>.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Written by Alan D. Salewski.
```


## Help

Basic program help is available via:

``` bash
$ nu --help
usage: nu [OPTION...] [FILE...]

Number input lines, emitting them to stdout. Output is modeled on that
of the 'set nu' command in the vi(1) editor (and many of its clones).

Mandatory arguments to long options are mandatory for short options too.

  -h, --help      Print this help message on stdout
  -V, --version   Print the version of the program on stdout
  -v, --verbose   Tell what is being done. Two or more -v options turns on tracing (set -x)
      --          Signals the end of options and disables further options processing.

Report bugs to Alan D. Salewski <salewski@att.net>
```


## Prerequisites

There are some prerequisite programs that are used, but nothing that shouldn't
be available on a typical GNU/Linux host (`'cat'`, `'sed'`, '`awk'`, '`nu`',
and a few others). Thus far the program has only been tested with the GNU
tools (`'bash'` 4.3.30, `'gawk'` 4.1.1, and the rest mostly from the
[GNU coreutils][GNU_COREUTILS] project).


# License

GPLv2+: GNU GPL version 2 or later <http://gnu.org/licenses/gpl.html>

Unless otherwise stated by a different license notice in a particular file,
all files in the `nu' project are made available under the GNU GPL version 2,
or (at your option) any later version.

See the [COPYING] file for the full license.

Copyright (C) 2016 Alan D. Salewski <salewski@att.net>

    This program is free software; you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation; either version 2 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License along
    with this program; if not, write to the Free Software Foundation, Inc.,
    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.



[BUGS]:          https://github.com/salewski/nu/blob/master/BUGS

[COPYING]:       https://github.com/salewski/nu/blob/master/COPYING

[EMACS]:         https://www.gnu.org/software/emacs/
[EMACS_LINENUM]: https://www.emacswiki.org/emacs/LineNumbers
[NL_WIKIPEDIA]:  https://en.wikipedia.org/wiki/Nl_(Unix)
[SETNU_EL]:      https://www.emacswiki.org/emacs/setnu.el
[SETNU_PLUS_EL]: https://www.emacswiki.org/emacs/setnu+.el
[VI_WIKIPEDIA]:  https://en.wikipedia.org/wiki/Vi
[GNU_COREUTILS]: https://www.gnu.org/software/coreutils/coreutils.html
