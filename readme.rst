
Cargo Out-Of-Source
===================

Script to help using cargo for out-of-source builds, written in Python3.

This wraps cargo, automatically using ``target`` path, next to the source tree.


Motivation
----------

Even when using ``.gitignore``, out-of-source files in the source tree can get in the way.

- Some tools don't respect ``.gitignore``, having to configure your tools
  to ignore files can be a hassle, especially for vim or emacs,
  where its likely multiple plugins need configuration.
- Some dependencies copy ``*.rs`` files into the target path,
  so recursive operations in Rust files in your source tree isn't guaranteed to *only* operate on your code.
- There is some chance running ``git-clean`` removes a file which was accidentally
  not committed into version control
  *(even manually checking the list of files is error prone since it can be very long)*.


Usage
-----

Simply add ``cargo-oos`` to your ``PATH`` and run ``cargo-oos`` instead of ``cargo``,

The target directory will be created along side your source-code directory with a ``-target`` suffix.
Out of source builds for any crate in a sub-directory of your source tree is also supported.

Examples of the source and destination names:


- ``/src/my_project/Cargo.toml`` -> ``/src/my_project-target``.
- ``/src/my_project/some_crates/Cargo.toml`` -> ``/src/my_project-some_crates-target``.
- ``/src/my_project/some_crates/other/Cargo.toml`` -> ``/src/my_project-some_crates-other-target``.


This can also run from a sub-directory, it searches up the parent directories,
running ``cargo`` in the first parent directory that contains a ``Cargo.toml``.


Examples
--------

Building works as you'd expect::

   cargo-oos build

Arguments are forwarded to cargo::

   cargo-oos run --release

Keep ``Cargo.lock`` in the build directory too::

   RELOCATE_FILES=1 cargo-oos build


Environment Variables
---------------------

Environment variables and their default values:

``CARGO_BIN=cargo``
   Cargo binary to run.
``VERBOSE=0``
   Set to ``1`` to print extra information from this utility.
   *(cargo behavior remains unchanged)*.
``RELOCATE_FILES=0``
   Keep files (currently only ``Cargo.lock``) in the build directory.

   Set to ``1`` if you don't commit this file into version control.


Details
-------

- Existing symlinks to executables can be used to detect existing out-of-source build directories.
  This must be located next to ``Cargo.toml``.

  Otherwise the default target-path is used.

- The source tree is detected by checking for ``.git``, ``.svn`` or ``.hg`` directories.
