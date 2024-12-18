# pwned-pw-download

Download the pwned password database into a 4 level tree.

It takes about 25 minutes to download the tree.

The tree contains 4369 directories and 1048576 files
not including the `curl.out` diagnostic files.


## TL;DR

To use, we suggest:

```sh
    ./pwned-pw-download -v 3 pwned.password.tree
```


## Usage

```
    usage: ./pwned-pw-download [-h] [-v level] [-V] topdir

        -h		print help message and exit
        -v level	set verbosity level (def level: 0)
        -V		print version string and exit

        topdir		top of the 4-level pwned password tree

        NOTE: It takes about 25 minutes to download the pwned password tree.

        NOTE: The tree contains 4369 directories and contains 1048576 files
              not including files such as curl.out and the top level repo files.

        NOTE: For more information see:

            https://github.com/lcn2/pwned-pw-download

    Exit codes:
         0         all OK
         1	       some internal tool exited non-zero
         2         -h and help string printed or -V and version string printed
         3         command line error
         4         bash version is too old
         5	       topdir alerady exists or cannot make topdir
         6         some curl command exited non-zer0
     >= 10         internal error

    pwned-pw-download version: 1.0 2024-12-17
```


## Why a 4 level tree

The `pwned-pw-download.sh` shell script will download the entire dataset used by
[HIBP](https://haveibeenpwned.com/Passwords) into a 4 level directory tree.

Unlike some downloads, this will will NOT form a huge directory, which for many
file systems is not very efficient.  Instead, we form a 4 level tree where
the bottom level directors contain only 256 files each, plus a `curl.out' file
for diagnostic purposes.


## Pwned password tree layout

The pwned password tree has 4 levels.  Files are of the form:

```
    i/j/k/ikjxy
```

where i, j, k, x, y are UPPER CASE hex digits:

```
    0 1 2 3 4 5 6 7 8 9 A B C D E F
```

Each file is of the form:

```
    35-UPPER-CASE-HEX-digis, followed by a colon (":"), followed by an integer > 0
```

For eample, all pwned passwords with a SHA-1 that begins with 12345 will be found in:

```
    1/2/3/12345
```

NOTE: The first 1 SHA-1 HEX characters are duplicated in the 3 directory levels.


## Example: a line from 1/2/3/12345

The `1/2/3/12345` contains the following line:

```
    00772720168B19640759677862AD5350374:4
```

The SHA-1 hash of the pwned password is the 1st 5 HEX digits from the file,
plus the 35 hex digits of the line and before the colon (":").  Thus the
SHA-1 hash of the pwned password is:

```
    1234500772720168B19640759677862AD5350374
```

The `4` after the colon (":") means that that given password has been pwned at
least 4 times and should **NOT** be used.


## Example: Lookup password in the tree

Consider the password:

```
    password
```

The SHA-1 hash of "`password`" is:

```
    5BAA61E4C9B93F3F0682250B6CF8331B7EE68FD8
```

Using the first 5 hex digits, open the file:

```
    5/B/A/5BAA6
```

Look for the remaining 35 hex digits followed by a :

```sh
    grep -F 1E4C9B93F3F0682250B6CF8331B7EE68FD8: 5/B/A/5BAA6
```

This will produce the line:

```
    1E4C9B93F3F0682250B6CF8331B7EE68FD8:10437277
```

This indicates that the password, password, has been pwned at least 10437277 times!