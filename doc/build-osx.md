Mac OS X monocled build instructions
====================================

Authors
-------

* Laszlo Hanyecz <solar@heliacal.net>
* Douglas Huff <dhuff@jrbobdobbs.org>
* Colin Dean <cad@cad.cx>
* Gavin Andresen <gavinandresen@gmail.com>

License
-------

Copyright (c) 2009-2012 Bitcoin Developers

Distributed under the MIT/X11 software license, see the accompanying
file COPYING or http://www.opensource.org/licenses/mit-license.php.

This product includes software developed by the OpenSSL Project for use in
the OpenSSL Toolkit (http://www.openssl.org/).

This product includes cryptographic software written by
Eric Young (eay@cryptsoft.com) and UPnP software written by Thomas Bernard.

Notes
-----

See `doc/readme-qt.rst` for instructions on building Monocle, the
graphical user interface.

Tested on OS X 10.5 through 10.10 on Intel processors only. PPC is not
supported because it is big-endian.

All of the commands should be executed in a Terminal application. The
built-in one is located in `/Applications/Utilities`.

Preparation
-----------

You need to install XCode with all the options checked so that the compiler
and everything is available in /usr not just /Developer. XCode should be
available on your OS X installation media, but if not, you can get the
current version from https://developer.apple.com/xcode/. If you install
Xcode 4.3 or later, you'll need to install its command line tools. This can
be done in `Xcode > Preferences > Downloads > Components` and generally must
be re-done or updated every time Xcode is updated.

There's an assumption that you already have `git` installed, as well. If
not, it's the path of least resistance to install [Github for Mac](https://mac.github.com/)
(OS X 10.7+) or
[Git for OS X](https://code.google.com/p/git-osx-installer/). It is also
available via Homebrew or MacPorts.

You will also need to install [Homebrew](http://mxcl.github.io/homebrew/)
or [MacPorts](https://www.macports.org/) in order to install library
dependencies. It's largely a religious decision which to choose, but, as of
December 2012, MacPorts is a little easier because you can just install the
dependencies immediately - no other work required. If you're unsure, read
the instructions through first in order to assess what you want to do.
Homebrew is a little more popular among those newer to OS X.

The installation of the actual dependencies is covered in the Instructions
sections below.

Instructions: MacPorts
----------------------

### Install dependencies

Installing the dependencies using MacPorts is very straightforward.

    sudo port install boost db48@+no_java openssl miniupnpc

### Building `monocled`

1. Clone the github tree to get the source code and go into the directory.

        git clone git@github.com:monocle/monocle.git monocle
        cd monocle

2.  Build monocled:

        cd src
        make -f makefile.osx

3.  It is a good idea to build and run the unit tests, too:

        make -f makefile.osx test

Instructions: HomeBrew
----------------------

#### Install dependencies using Homebrew

        brew install boost miniupnpc openssl berkeley-db4 gmp

#####Install secp256k1

        wget https://github.com/bitcoin/secp256k1/archive/3545627acb6059832834569829e97fb469933b29.zip
        cd secp256k1-3545627acb6059832834569829e97fb469933b29
        sh autogen.sh
        ./configure
        make
        sudo make install

Note: After you have installed the dependencies, you should check that the Brew installed version of OpenSSL is the one available for compilation. You can check this by typing

        openssl version

into Terminal. You should see OpenSSL 1.0.1j 15 Oct 2014.

If not, you can ensure that the Brew OpenSSL is correctly linked by running

        brew link openssl --force

Rerunning "openssl version" should now return the correct version.

### Building `monocled`

1. Clone the github tree to get the source code and go into the directory.

        git clone git@github.com:monocle/monocle.git monocle
        cd monocle

2.  Build monocled:

        cd src
        make -f makefile.osx

4.  It is a good idea to build and run the unit tests, too:

        make -f makefile.osx test

Creating a release build
------------------------

A monocled binary is not included in the Monocle.app bundle. You can ignore
this section if you are building `monocled` for your own use.

If you are building `litecond` for others, your build machine should be set up
as follows for maximum compatibility:

All dependencies should be compiled with these flags:

    -mmacosx-version-min=10.5 -arch i386 -isysroot /Developer/SDKs/MacOSX10.5.sdk

For MacPorts, that means editing your macports.conf and setting
`macosx_deployment_target` and `build_arch`:

    macosx_deployment_target=10.5
    build_arch=i386

... and then uninstalling and re-installing, or simply rebuilding, all ports.

As of December 2012, the `boost` port does not obey `macosx_deployment_target`.
Download `http://gavinandresen-bitcoin.s3.amazonaws.com/boost_macports_fix.zip`
for a fix. Some ports also seem to obey either `build_arch` or
`macosx_deployment_target`, but not both at the same time. For example, building
on an OS X 10.6 64-bit machine fails. Official release builds of Monocle are
compiled on an OS X 10.6 32-bit machine to workaround that problem.

Once dependencies are compiled, creating `Monocle.app` is easy:

    make -f makefile.osx

Running
-------

It's now available at `./monocled`, provided that you are still in the `src`
directory. We have to first create the RPC configuration file, though.

Run `./monocled` to get the filename where it should be put, or just try these
commands:

    echo -e "rpcuser=monoclerpc\nrpcpassword=$(xxd -l 16 -p /dev/urandom)" > "/Users/${USER}/Library/Application Support/Monocle/monocle.conf"
    chmod 600 "/Users/${USER}/Library/Application Support/Monocle/monocle.conf"

When next you run it, it will start downloading the blockchain, but it won't
output anything while it's doing this. This process may take several hours.

Other commands:

    ./monocled --help  # for a list of command-line options.
    ./monocled -daemon # to start the monocle daemon.
    ./monocled help    # When the daemon is running, to get a list of RPC commands
