This C program parses a Windows Address Book (.WAB) file and prints it to the screen in a nice human-readable format. It should work on any unix-based system with a C compiler.

*Note:* It is Apr 2022. I'm on MacOS 11.6.5, using the `zsh` shell and this version of `gcc`:

```shell
$ gcc --version
Configured with: --prefix=/Library/Developer/CommandLineTools/usr --with-gxx-include-dir=/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/c++/4.2.1
Apple clang version 12.0.0 (clang-1200.0.32.2)
Target: x86_64-apple-darwin20.6.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin
```

# Installation and usage:

## Easy

Get the source, compile it, and run it, like this:

```shell
$ git clone git@github.com:justinpearson/libwab.git
$ cd libwab
$ gcc -c cencode.c libwab.c pstwabids.c tools.c uerr.c wabread.c
$ gcc -o wabread cencode.o libwab.o pstwabids.o tools.o uerr.o wabread.o -liconv 
$ ./wabread address_book-2003-11-08.WAB

# Bob Johnson
dn: cn=Bob Johnson
cn: Bob Johnson
mail: bob@johnson.net

# Alice Wilson
dn: cn=Alice Wilson
cn: Alice Wilson
mail: alice@wilson.edu

...

```

**Note:** The above approach does not support unicode in the WAB file. So when compiling `libwab.c` you will get an error

```
libwab.c:17:2: warning: "ICONV IS MISSING!  Unicode will be BADLY broken" [-W#warnings]
```

but the final program still worked for me.




## Need unicode support? Use `./configure`

The very first commit in this repo included files to build the project with the `./configure && make ` toolchain. Later commits replaced those with cmake files, but I don't have cmake, so I restored those files and tweaked `libwab.c` to conditionally include the `config.h` supplied by `make`.

This is more complex than just using `gcc`, but it includes`iconv.h` if the OS has it, which is needed for unicode support.

```shell
$ git clone git@github.com:justinpearson/libwab.git
$ cd libwab
$ ./configure
$ make   # no "iconv missing" error :)
$ ./wabread address_book-2003-11-08.WAB

# Bob Johnson
dn: cn=Bob Johnson
cn: Bob Johnson
mail: bob@johnson.net

# Alice Wilson
dn: cn=Alice Wilson
cn: Alice Wilson
mail: alice@wilson.edu

...
```





## Using cmake

If you need unicode support and you have `cmake`, try this:

```shell
$ git clone git@github.com:justinpearson/libwab.git
$ cd libwab
$ cmake  # or maybe you need to run `cmake libwab` in parent dir?
$ make
$ ./wabread address_book-2003-11-08.WAB
```

(I haven't tested this.)



# Background

I forked this repo from [pboettch/libwab](https://github.com/pboettch/libwab) and made the following changes to simplify it:

1. Remove `#include <malloc.h>` from `tools.c` to avoid compiler error. It still compiled: apparently `malloc()` is included in `stdlib.h` anyway, see [here](https://stackoverflow.com/questions/56463049/should-mac-osx-have-a-malloc-h-file).

2. Copied `./configure`-based installation files from original commit, and tweaked `libwab.c` to conditionally use the generated `config.h` file.

3. Improved README.



# Previous README:

```

PREFACE
-------

	On 2016-02-13 I needed a Windows Address Book export-tool in Linux. Unable
	to find anything quickly I found traces of existance of this code on the web.

	After some digging and more digging, google came up with a source-RPM which
	contained this code.

	I removed the configure-stuff and added a CMakeLists.txt . This and
	publishing it on guthub is my contribution.

	-Patrick Boettcher

INTRODUCTION
------------

	Windows includes a system to store email addresses and other contact
	information known as the Windows Address Book (wab).  This is used by
	Outlook Express.

	Libwab turns windows address book (wab) files into ldif files.  ldif
	files are a nice plain-text format that can easily be comprehended and
	manipulated by humans.  ldif can also be imported into and exported
	from any email software that isn't complete garbage.

	BUT...I have never tried to load the output with anything.  If you have
	success then send me an email and let me know.

	You can, using heuristic mode (see below), retrieve deleted records
	from a .wab file.  You can also (maybe) retrieve records from a
	damanged .wab file.

	Anyhow - there's no man page, no configure and --help will probably lie
	to you.


BUILDING
--------

	Create a build-dir somewhere
	Run 'cmake <path-to-libwab>' in build dir
	Run 'make'

USING
-----

	Wabread will excrete output to stdout.  So you'll want to do something
	like this:

	$ ./wabread mywabfile.wab >mywabdata.ldif

	For more information run wabread with nothing on the command line.

	$ ./wabread

	Use:  wabread [options] <filename.wab>

	  Options:
	   -d #        set debugging (logical or 1,2,3,4...)
	   -h          heuristic record dump: attempt to
			 recover a broken .wab file
	   -c          display extra crud.


HEURISTIC MODE
--------------

	Normally libwab will try to read some indexes at the start of the wab
	file.  It will then print the records that are listed in this index.

	If a record is deleted from the index then libwab will not, by default,
	print it.  Also if the index is damaged then libwab will probably crash
	while reading the file.

	If you have a damanged .wab file or your have deleted addresses then
	you can use heuristic mode.

	Heuristic mode will search through the entire file searching for things
	that look like wab records.  It will output what it finds.  This will
	include deleted records which have not been overwritten.

	To use this mode pass wabread an '-h' like this:

	$ ./wabread -h mywabfile.wab >mywabdata.ldif

IF HEURISTIC MODE FAILS
-----------------------

	If libwab does not return the data you are looking for in heuristic
	mode then there is one more thing you can do.  You'll need a unix
	environment for this.  If you are silly enough to be running windows
	then you can get a decent unix environment from either Cygwin or the
	Native Windows Ports of Unix Utilities.  Look here:

		http://www.cygwin.com/
		http://unxutils.sourceforge.net/

	Or use google.

	Once you have a little unix environment then do this:

		cat YourWabFile.wab |tr -d '\000'|strings|less

	This will puke all of the printable strings in your .wab file.  If your
	data are not in the output of this command then they are probably gone.
	The only thing you can try beyond this is a hex editor...but it's very
	unlikely that you'll find anything good.


MISC HISTORY NOISE
------------------

I wrote this program a while ago in order to automate the conversion of outlook
express .wab files to Thunderbird format files.  When I was writing this pile
of crap I didn't realize that Thunderbird stores mail in "mork" format.

Dealing with Mork turned out to be WORSE THAN REVERSE-ENGINEERING THE BINARY
.WAB FILE FORMAT.

Update: I have mostly given up on dealing with Microsoft systems.  They really
aren't worth the time.  I will try to continue to maintain the software as
people send me patches and bitch at me.  However I make no promises.

Enjoy!


Sean Loaring

email (I'm sure you can figure it out):
  sloaring      is my name
  AT            is the text version of an ampersand
  tec-man       is my email domain
  dawt          is a misspelling of dot
  com           is the end

```