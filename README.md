![GitHub release (latest by date)](https://img.shields.io/github/v/release/faf0/sct)
![GitHub Release Date](https://img.shields.io/github/release-date/faf0/sct)
![GitHub repo size](https://img.shields.io/github/repo-size/faf0/sct)
![GitHub](https://img.shields.io/github/license/faf0/sct)  

# About

Xsct (X11 set color temperature) is a UNIX tool which allows you to set the color
temperature of your screen. It is simpler than [Redshift](https://github.com/jonls/redshift) and [f.lux](https://justgetflux.com/).

Original code was published by Ted Unangst in the public domain:
https://www.tedunangst.com/flak/post/sct-set-color-temperature

Minor modifications were made in order to get sct to:
- compile on Ubuntu 14.04 and later releases
- iterate over all screens of the default display and change the color
  temperature
- free the Display structure
- fix memleaks
- clean up code
- return `EXIT_SUCCESS`

# Installation

## Make-based

On UNIX-based systems, a convenient method of building this software is using Make.
Since the `Makefile` is simple and portable, both the BSD and [GNU make](https://www.gnu.org/software/make/) variants will have no problems parsing and executing it correctly.

The simplest invocation is
~~~sh
make
~~~
that will use the default values for all flags as provided in the `Makefile`.

Overriding any of the following variables is supported by exporting a variable with the same name and your desired content to the environment:
* `CC`
* `CFLAGS`
* `LDFLAGS`
* `PREFIX`
* `BIN` (the directory into which the resulting binary will be installed)
* `MAN` (the directory into which the man page will be installed)
* `INSTALL` (`install(1)` program used to create directories and copy files)

Both example calls are equivalent and will build the software with the specified compiler and custom compiler flags:
~~~sh
make CC='clang' CFLAGS='-O2 -pipe -g3 -ggdb3' LDFLAGS='-L/very/special/directory -flto'
~~~

~~~sh
export CC='clang'
export CFLAGS='-O2 -pipe -g3 -ggdb3'
export LDFLAGS='-L/very/special/directory -flto'
make
~~~

The software can be installed by running the following command:
~~~sh
make install
~~~

If you prefer a different installation location, override the `PREFIX` variable:
~~~sh
make install PREFIX="${HOME}/xsct-prefix"
~~~

~~~sh
export PREFIX="${HOME}/xsct-prefix"
make install
~~~

## Manual compilation

Compile the code using the following command:
~~~sh
gcc -Wall -Wextra -Werror -pedantic -std=c99 -O2 -I /usr/X11R6/include xsct.c -o xsct -L /usr/X11R6/lib -lX11 -lXrandr -lm -s
~~~

# Usage

Provided that xsct binary is located in your `$PATH`, execute it using the following command:
~~~sh
xsct 3700 0.9
~~~

The first parameter (`3700` above) represents the color temperature.

The second parameter (`0.9` above) represents the brightness. The values are in the range `[0.0, 1.0]`.

If `xsct` is called with parameter 0, the color temperature is set to `6500`.

If `xsct` is called with the color temperature parameter only, the brightness is set to `1.0`.

If `xsct` is called without parameters, the current display temperature and brightness are estimated.

The following options, which can be specified before the optional temperature parameter, are supported:
- `-h`, `--help`: display the help page
- `-v`, `--verbose`: display debugging information
- `-d <delta>`, `--delta <delta>`: temperature and brightness are shifted relatively and not absolutely
- `-s <screen>`, `--screen <screen>` `N`: use the screen specified by given zero-based index
- `-t`, `--toggle`: toggle between night and day temperature
- `-c <crtc>`, `--crtc <crtc>` `N`: use the CRTC specified by given zero-based index

Here are a few examples of how to control the brightness and the temperature using `xsct`:

| Command             | Explanation                                                             |
|---------------------|-------------------------------------------------------------------------|
| `xsct`              | Estimate the current display temperature and brightness                 |
| `xsct 0`            | Set the temperature to `6500` and the brightness to `1.0` (default)     |
| `xsct 3700 0.9`     | Set the temperature to `3700` and the brightness to `0.9`               |
| `xsct 3700`         | Set the temperature to `3700` and the brightness to `1.0` (default)     |
| `xsct -d 1000`      | Increase the temperature by `1000`                                      |
| `xsct -d -500`      | Decrease the temperature by `500`                                       |
| `xsct -d 1000 0.2`  | Increase the temperature by `1000` and increase the brightness by `0.2` |
| `xsct -d -500 -0.3` | Decrease the temperature by `500` and decrease the brightness by `0.3`  |
| `xsct -d 0 0.2`     | Increase the brightness by `0.2`                                        |

Test xsct using the following command:
~~~sh
xsct 3700 0.9 && xsct
~~~

# Quirks

If the delta mode is used to decrease the brightness to below 0.0 and then increased above 0.0, the temperature will reset to 6500 K, regardless of whatever it was before the brightness reached 0.
This is because the temperature is reset to 0 K when the brightness is set equal to or below 0.0 (to verify this, you can run `xsct 0 0.0; xsct`).

# Resources

The following website by Mitchell Charity provides a table for the conversion between black-body temperatures and color pixel values:
http://www.vendian.org/mncharity/dir3/blackbody/

---

https://github.com/faf0/sct/
