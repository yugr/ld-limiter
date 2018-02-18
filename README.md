# Motivation

One frequent problem when building large applications is limiting number of link jobs that are run in parallel. Linkers tend to consume too much memory and cause OS to become unresponsive or even OOM. Traditional `make` does not allow you to do this (`-j` or `-l` will also limit parallel compiler runs).

You can switch to more modern build systems (e.g. Ninja) or use `ld-limit`.

`Ld-limit` provides an easy workaround - it wraps linker executables (`ld`, etc.) into scripts which check how many jobs were already started and wait until number of jobs is sufficiently small.

# Usage

Simply run

```
$ ld-limit make ...
```
This will run at most _one_ linker at any moment in time.

To allow more than one linker do
```
$ ld-limit -n 2 make ...
```

When two (or more) `ld-limit`ed jobs are run in parallel, they will have independent job limits. So e.g. in the following case

```
$ ld-limit -n 1 make ... &
$ ld-limit -n 1 make ...
```

may cumulatively run up to 2 parallel link jobs.

# Limitations

`Ld-limit` is only lightly tested atm (please send bug reports).

The tool is implemented in pure shell to achieve maximum portability (e.g. with Cygwin). This makes it less efficient than necessary (e.g. instead of blocking on a true mutex stopped processes busy wait).
