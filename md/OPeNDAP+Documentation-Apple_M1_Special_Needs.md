Computers with the Apple M1 chip require dedicated binaries, or binaries
with both Intel and M1 contents. In order to get the hyrax-dependencies
to build the following packages need to be installed prior to running
the hyrax-dependencies build.

## bison

- brew install bison
- export PATH="/opt/homebrew/opt/bison/bin:\$PATH"

## openssl

- brew install openssl
- export CPPFLAGS="-I/opt/homebrew/Cellar/openssl@3/3.0.1/include"
- export LDFLAGS="-L/opt/homebrew/Cellar/openssl@3/3.0.1/lib"
- sudo ln -s /opt/homebrew/Cellar/openssl@3/3.3.0
  /usr/local/opt/opensslexport

## jpg

- brew install jpg
- export PATH="/opt/homebrew/opt/jpeg/bin:\$PATH"
- export LDFLAGS="-L/opt/homebrew/opt/jpeg/lib \$LDFLAGS"
- export CPPFLAGS="-I/opt/homebrew/opt/jpeg/include \$CPPFLAGS"

## libpng

This is needed to build the gdal library in the hyrax-dependencies.

- [Download libpng source](http://www.libpng.org/pub/png/libpng.html)

Unpack the distribution bundle and build and install the library into
/usr/local

- ./configure
- make
- make check
- make install