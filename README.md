# pymakeself

Make self-extracting archives with Python, on most operating systems.

## Overview

pymakeself is a Python script that generates a self-extractable tar.gz archive from a directory.  The resulting file appears as a Python script, and can be launched as is.  The archive will then uncompress itself to a temporary directory and run an optional python setup script.  pymakeself archives also include a MD5 checksum for integrity self-validation.

The makeself.py script itself is used only to create the archive from a directory of files.  The resultant archive is actually a compressed (gzip or bzip2) TAR archive, with a small Python script stub at the beginning.  This small stub performs all the steps of extracting the files, running the embedded setup script, and removing the temporary files when it's all over.  All that the user has to do to install the software contained in such an archive is to "run" the archive, i.e python nice-software.py.

This code is intended to be as portable as possible and should run on any system with an installation of python2.7 or later.  Othen than Python, it does not rely on external utilities such as tar, gzip, bash etc.

As of version 0.1.0, PyMakeSelf has been tested on the following platforms:

- Linux (all distributions)
- FreeBSD

Testing with these whould be done soon:

- Windows7
- MacOS X (Darwin)

## Usage

The syntax of makeself.py is the following:
makeself.py [args] content_dir file_name [setup_script [script_args]]

args are optional options for Makeself.  The available ones are:

`--version` : Prints the version number on stdout, then exits immediately

`--help, -h` : Print out this help message.

`--quiet, -q` : Do not print any messages other than errors.

`--tools, -t`  : Include installtools module.

`--gzip` : Use gzip for compression (is the default)

`--bzip2` : Use bzip2 instead of gzip for better compression.

`--xz` : Compress using xz instead of gzip.  This requires Python3.x for both creation and extraction.

`--current` : Extract to current directory, instead of to a temporary.

`--target dir` : Extract directly to a target directory, instead of to a temporary directory (temporary is default).

`--follow` : Follow the symbolic links inside of the archive directory, i.e. store the files that are being pointed to instead of the links themselves.

`--nomd5` : Disable the creation of a MD5 checksum for the archive.  This speeds up the extraction process if integrity checking is not necessary.

content_dir is the name of the directory that contains the files to be archived

file_name is the name of the installer script to be created

setup_script is a Python script to be executed from within the extracted content directory, that is run using the same Python interpreter used to run the installer.  If the script is already located inside the content directory then only specify the name of the script.  Otherwise, provide a relative or absolute path to the script so that it can be copied into the installer archive.  The special value "@accountutil" tells pymakeself to use the UNIX account creation tool, included in the pymakeself package, as the setup_command.

Here is an example, assuming the user has a package image stored in a /home/joe/mysoft, and he wants to generate a self-extracting package named install_mysoft.py, which will launch the "setup.py" script initially stored in /home/joe/mysoft:
pymakeself.py --label "Joe's Nice Software Package" /home/joe/mysoft install_mysoft setup.py

Here is also how I created the install_pymakeself.py installer that installs the pymakeself distribution:
```
python -m pymakeself --label "PyMakeSelf by Andrew Gillis" pymakeself install_pymakeself setup.py install
```

Archives generated with pymakeself can be passed the following arguments:

`--check` : Check the archive's MD5 sum and exit.

`--extract` : Extract package contents to temproary directory and exit.

Any subsequent arguments to the archive will be passed as additional arguments to the embedded command.

## Examples

Create an installer, named install_stuff, that runs setup.py:

```
python -m pymakeself /storage/myfiles install_stuff setup.py
```

Create an installer that runs the accountutil.py tool (one of the modules in the pymakeself installtools) as the setup script, to create the "ajg" user account:
```
python -m pymakeself ~/ajg_dot_files create_ajg @accountutil \
-c 'Andrew J. Gillis' -i ./ ajg
```
Specifying `@accountutil` as the setup script tells pymakeself to use the UNIX account creation tool, that is included with the pymakeself package.

Notice that the `-i` argument to accountutil specifies "./" instead of "ajg_dot_files".  This is because the setup file is always run from within the archive directory.

## Project Links

- Project page: <https://github.com/gammazero/pymakeself>
- Documentation: <https://github.com/gammazero/pymakeself/wiki>
- License: <http://www.opensource.org/licenses/mit-license.php>

## Bugs and Issues

<https://github.com/gammazero/pymakeself/issues>

## Acknowledgements

This script was inspired by, and modeled after, makeself by Stephane Peter.
