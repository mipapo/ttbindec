
Table of Contents
=================

  * [TTBIN Decoder](#ttbin-decoder)
    * [Usage](#usage)
    * [Unknown record types](#unknown-record-types)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)
# TTBIN Decoder

This is a utility to help delve into the details of the TTBIN file
format produced by TomTom GPS Sport Watches.

It is based on source code from
[`ryanbinns/ttwatch`](http://github.com/ryanbinns/ttwatch), which I have forked as
[`dlenski/ttwatch`](http://github.com/dlenski/ttwatch).

The `ttwatch` source code describes the basic outlines of the file
format, which consists of a series of records introduced by one-byte
tags (e.g. tag `0x20` indicates the file-header record, which
immediately follows the tag byte).

## Usage

```
$ ttbindec.py ActivityFile.ttbin
```

Decodes the contents of a .ttbin file (currently *only* supports `file_version=7`,
as produced by recent watch firmware versions such as v1.8.25 or v1.8.42).

In the case of corrupt files, it will make a very great effort to skip
over junk and label it in the output (**this is the reason why I
originally wrote this tool**)!

```
$ ttbin_summary.py ActivityFile.ttbin
```

Prints a brief summary of the contents of a .ttbin file, and can
reverse-geocode them to get approximate starting location using Google
Maps API. Can also rename files to a naturally-sorting name format
with the `--rename`/`-r` option
(`YYYY-MM-DDTHH:MM:SS_Activity_Duration.ttbin`).

```
$ ttbin_scrape_defs.py # defaults to current directory, defs.py
$ ttbin_scrape_defs.py --help
```

Regenerates `defs.py`, which contains the definitions for the various
in-file structures, required by `ttbindec.py`. This program scrapes
`ttwatch/ttbin/ttbin.c` and `ttwatch/ttbin/ttbin.h` to obtain the
definitions.

If you append the file [`ttbin-magic`](ttbin-magic) to `~/.magic`, the
[`file(1)`](http://en.wikipedia.org/wiki/File_%28command%29) utility
will be able to identify TTBIN files (version 7 only) and their basic
details:

```sh
$ cat ttbin-magic >> ~/.magic         # only needs to be done once!
$ file *.ttbin
0x00910000_20150807_123616.ttbin:     TomTom activity file, v7 (Fri Aug  7 11:49:22 2015, device firmware 1.8.42, product ID 1001)
```

## Unknown record types

Currently, these are the unknown tags/records which are reported to exist in
the TTBIN files produced by my own TomTom Runner GPS watch with
firmware v1.8.25. **Please let me know if you have any information on
their meaning.**

* `0x30` appears exactly once in each TTBIN file for an outdoor run,
immediately following the `FILE_HEADER` and `RECORD_LENGTH` records.

```
30 RECORD_LENGTH(tag=48, length=3)     [ UNKNOWN_0x30 ]
```

* `0x23` Appears subsequent to **every single** `FILE_GPS_RECORD`. Values seem to
change suddenly in areas where GPS signal is shaky, and stabilize
towards the end of long runs.

```
23 RECORD_LENGTH(tag=35, length=20)    [ UNKNOWN_0x23 ]
```

* `0x37` appears exactly once in each TTBIN file for an outdoor run,
immediately prior to the *second* `FILE_GPS_RECORD`, always with the
byte value `0x01`.

```
37 RECORD_LENGTH(tag=55, length=2)     [ UNKNOWN_0x37 ]
```

* `0x31` doesn't appear in any of my activity files.

```
31 RECORD_LENGTH(tag=49, length=11)    [ UNKNOWN_0x31 ]
```

