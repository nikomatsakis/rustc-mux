# rustc-mux

A rustup-compatible rustc toolchain that selects which version of `rustc` to use based on your local directory.

### To install

Clone this repo and change into the repo directory:

```
git clone git@github.com:nikomatsakis/rustc-mux.git
cd rustc-mux
```

Then execute:

```
rustup toolchain link mux $PWD
```

This will create a new toolchain, called `mux`, then selects your
rustc dynamically based on your current directory. You may wish to
then change your default to use `mux` everywhere:

```
rustup default mux
```

Alternatively, you can change to particular directories and set
your default in just those directories:

```
cd /path/to/some/rustc/checkout
rustup override set mux
```

Or you can just select to use `mux` on any individual execution:

```
rustc +mux ...
```

### How `rustc` selects which rust executable to use by default

When you run `rustc`, the wrapper will first walk up from your current
directory, seeking a clone of the rust repo. It will prefer to
execute, in this order:

- a stage2 build found in some parent directory, or
- a stage1 build found in some parent directory.

If it fails to find either of those, it will error out. If the clone of
rust is not in a parent of the current directory, you can specify it
manually using the `RUST_ROOT` environment variable.

Example:

```
# After executing:
#   git clone https://github.com/rust-lang/rust/
RUST_ROOT=$PWD/rust rustc ...
```

### Options

In addition to the standard `rustc` options, there are a number of
other options you can use when using `mux`:

```
rustc --echo ...
```

Adding `--echo` will cause rustc-mux not to execute rustc, but instead
just to print exactly what it *would have* executed. Useful for debugging
your setup.

```
rustc --stage0 ...
rustc --stage1 ...
rustc --stage2 ...
```

Specifying `--stageN` will select manually which compiler to
use. stage0 is the "snapshot", or the current beta. stage1 is the
result of building your working directory with stage0, and stage2 is
the result of building your working directory with itself. The default
is to use the maximum stage we can find.

```
rustc --gdb ...
rustc --lldb ...
```

This will launch rustc running in your current directory.

### Disclaimer

This script is a total hack. It works for me, mostly, but I'd welcome
suggestions for improvement!

