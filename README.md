cmark-lemmy
=============

> [!note]
> This project has been replaced with [LemmyMarkdownUI](https://github.com/mlemgroup/LemmyMarkdownUI). The cmark code is contained within the `Sources/cmark-lemmy` folder of that repository. The cmark code used in LemmyMarkdownUI is based on [cmark-gfm](https://github.com/github/cmark-gfm), whereas the code in this repo was based on just [cmark](https://github.com/commonmark/cmark). Basing the code on cmark-gfm ended up being a better option because it contains support for some Lemmy-flavored features (e.g. tables) already.

A modified version of [cmark](https://github.com/commonmark/cmark) for parsing [Lemmy-flavored markdown](https://join-lemmy.org/docs/users/02-media.html#text). The rest of this README remains unchanged.

cmark
=====

[![CI
tests](https://github.com/commonmark/cmark/workflows/CI%20tests/badge.svg)](https://github.com/commonmark/cmark/actions)

`cmark` is the C reference implementation of [CommonMark], a
rationalized version of Markdown syntax with a [spec][the spec].
(For the JavaScript reference implementation, see
[commonmark.js].)

It provides a shared library (`libcmark`) with functions for parsing
CommonMark documents to an abstract syntax tree (AST), manipulating
the AST, and rendering the document to HTML, groff man, LaTeX,
CommonMark, or an XML representation of the AST.  It also provides a
command-line program (`cmark`) for parsing and rendering CommonMark
documents.

Advantages of this library:

- **Portable.**  The library and program are written in standard
  C99 and have no external dependencies.  They have been tested with
  MSVC, gcc, tcc, and clang.

- **Fast.** cmark can render a Markdown version of *War and Peace* in
  the blink of an eye (127 milliseconds on a ten year old laptop,
  vs. 100-400 milliseconds for an eye blink).  In our [benchmarks],
  cmark is 10,000 times faster than the original `Markdown.pl`, and
  on par with the very fastest available Markdown processors.

- **Accurate.** The library passes all CommonMark conformance tests.

- **Standardized.** The library can be expected to parse CommonMark
  the same way as any other conforming parser.  So, for example,
  you can use `commonmark.js` on the client to preview content that
  will be rendered on the server using `cmark`.

- **Robust.** The library has been extensively fuzz-tested using
  [american fuzzy lop].  The test suite includes pathological cases
  that bring many other Markdown parsers to a crawl (for example,
  thousands-deep nested bracketed text or block quotes).

- **Flexible.** CommonMark input is parsed to an AST which can be
  manipulated programmatically prior to rendering.

- **Multiple renderers.**  Output in HTML, groff man, LaTeX, CommonMark,
  and a custom XML format is supported. And it is easy to write new
  renderers to support other formats.

- **Free.** BSD2-licensed.

It is easy to use `libcmark` in python, lua, ruby, and other dynamic
languages: see the `wrappers/` subdirectory for some simple examples.

There are also libraries that wrap `libcmark` for
[Go](https://github.com/rhinoman/go-commonmark),
[Haskell](https://hackage.haskell.org/package/cmark),
[Ruby](https://github.com/gjtorikian/commonmarker),
[Lua](https://github.com/jgm/cmark-lua),
[Perl](https://metacpan.org/release/CommonMark),
[Python](https://pypi.python.org/pypi/paka.cmark),
[R](https://cran.r-project.org/package=commonmark) and
[Scala](https://github.com/sparsetech/cmark-scala).

Installing
----------

Building the C program (`cmark`) and shared library (`libcmark`)
requires [cmake].  If you modify `scanners.re`, then you will also
need [re2c] \(>= 0.14.2\), which is used to generate `scanners.c` from
`scanners.re`.  We have included a pre-generated `scanners.c` in
the repository to reduce build dependencies.

If you have GNU make, you can simply `make`, `make test`, and `make
install`.  This calls [cmake] to create a `Makefile` in the `build`
directory, then uses that `Makefile` to create the executable and
library.  The binaries can be found in `build/src`.  The default
installation prefix is `/usr/local`.  To change the installation
prefix, pass the `INSTALL_PREFIX` variable if you run `make` for the
first time: `make INSTALL_PREFIX=path`.

For a more portable method, you can use [cmake] manually. [cmake] knows
how to create build environments for many build systems.  For example,
on FreeBSD:

    mkdir build
    cd build
    cmake ..  # optionally: -DCMAKE_INSTALL_PREFIX=path
    make      # executable will be created as build/src/cmark
    make test
    make install

Or, to create Xcode project files on OSX:

    mkdir build
    cd build
    cmake -G Xcode ..
    open cmark.xcodeproj

The GNU Makefile also provides a few other targets for developers.
To run a benchmark:

    make bench

For more detailed benchmarks:

    make newbench

To run a test for memory leaks using `valgrind`:

    make leakcheck

To reformat source code using `clang-format`:

    make format

To run a "fuzz test" against ten long randomly generated inputs:

    make fuzztest

To do a more systematic fuzz test with [american fuzzy lop]:

    AFL_PATH=/path/to/afl_directory make afl

Fuzzing with [libFuzzer] is also supported. The fuzzer can be run with:

    make libFuzzer

To make a release tarball and zip archive:

    make archive

Installing (Windows)
--------------------

To compile with MSVC and NMAKE:

    nmake /f Makefile.nmake

You can cross-compile a Windows binary and dll on linux if you have the
`mingw32` compiler:

    make mingw

The binaries will be in `build-mingw/windows/bin`.

Usage
-----

Instructions for the use of the command line program and library can
be found in the man pages in the `man` subdirectory.

Security
--------

By default, the library will scrub raw HTML and potentially
dangerous links (`javascript:`, `vbscript:`, `data:`, `file:`).

To allow these, use the option `CMARK_OPT_UNSAFE` (or
`--unsafe`) with the command line program. If doing so, we
recommend you use a HTML sanitizer specific to your needs to
protect against [XSS
attacks](http://en.wikipedia.org/wiki/Cross-site_scripting).

Contributing
------------

There is a [forum for discussing
CommonMark](http://talk.commonmark.org); you should use it instead of
github issues for questions and possibly open-ended discussions.
Use the [github issue tracker](http://github.com/commonmark/CommonMark/issues)
only for simple, clear, actionable issues.

Authors
-------

John MacFarlane wrote the original library and program.
The block parsing algorithm was worked out together with David
Greenspan. Vicent Marti optimized the C implementation for
performance, increasing its speed tenfold.  Kārlis Gaņģis helped
work out a better parsing algorithm for links and emphasis,
eliminating several worst-case performance issues.
Nick Wellnhofer contributed many improvements, including
most of the C library's API and its test harness.

[benchmarks]: benchmarks.md
[the spec]: http://spec.commonmark.org
[CommonMark]: http://commonmark.org
[cmake]: http://www.cmake.org/download/
[re2c]: http://re2c.org
[commonmark.js]: https://github.com/commonmark/commonmark.js
[american fuzzy lop]: http://lcamtuf.coredump.cx/afl/
[libFuzzer]: http://llvm.org/docs/LibFuzzer.html
