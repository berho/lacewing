liblacewing is a library for writing cross-platform, networked applications in C,
C++ and Javascript.

http://github.com/udp/lacewing/

http://lacewing-project.org


Installing
==========

On Windows, use the MSVC project file in the 'msvc' directory to build a DLL/LIB.

On Linux/BSD/OS X:

    ./configure
    make
    make install
    
Node.JS module:

    node-waf configure build
    node-waf install
    
Android NDK (libssl.so/libcrypto.so are required - see docs/android_build.txt):

    cd jni
    ndk-build


Changes in 0.2.3 (2011-09-07)
=============================

- Fixed an issue with Lacewing::EventPump on Windows, which would cause functions queued with
  Post() not to execute in some cases.  This lead to to a significant issue with client disconnects
  not being detected properly in Lacewing::Server.
  
- Miscellaneous relay protocol fixes

    
Changes in 0.2.2 (2011-08-31)
=============================

- Lacewing::Timer now ends the timer thread properly in the destructor.  In previous
  versions it was necessary to call Stop() explicitly to avoid a crash.
  
- Two issues with the EventPump class fixed:

    * StartEventLoop() could return prematurely in some cases (Unix only)
    * Possible segmentation fault when a null WriteCallback was specified

- Added the RelayClient and RelayServer source files to the Makefile and Android.mk

- SHA1_Hex no longer cuts off the last four bytes

- Updated relay protocol specification and implementation classes (now revision 3)

- Fixed issue with the GuessMimeType function returning application/octet-stream even
  for known file extensions

- Refactored the internals of Lacewing::Webserver for future SPDY support.  The HTTP
  specific code is now separate from the webserver itself.


Changes in 0.2.1 (2011-07-15)
=============================

- Fixed crash in the List utility class introduced by 0.2.0


Changes in 0.2.0 (2011-07-14)
=============================

- New Javascript liblacewing API for MozJS (SpiderMonkey/TraceMonkey) and V8.
  Currently only supports the webserver and utility classes.  It is also
  now possible to build liblacewing as a Node.JS module - a wscript for node-waf
  is provided.
  
- Added support for building for Android (Android.mk is in the 'jni' folder)

- The build system has been changed from automake to autoconf with a simple
  Makefile.  This should solve the headaches with building on different
  platforms (Windows users should still use the MSVC project as normal.) 

- Removed the dependency on STL, resulting in a much faster build and smaller
  binaries (particularly on Android.)  This also makes it easier to link
  liblacewing into static Linux binaries.

- New global hash functions, implemented via OpenSSL or the Win32 crypt32 API:

    * Lacewing::MD5
    * Lacewing::MD5_Hex
    * Lacewing::SHA1
    * Lacewing::SHA1_Hex
    
- PostEventLoopExit() function added to the EventPump, to cause StartEventLoop()
  to return (once any pending events have been processed.)

- In the Webserver, Request objects are now freed immediately after the
  connection is closed, after calling the disconnect handler.  Previously, the
  Request object would remain in a disconnected state until Finish() was called.

- In Unix, the EventPump class is is now just a default implementation of the
  Pump class, which can be derived from to use custom methods of watching FDs.
  A custom pump using libev is provided in the 'ev' folder.

- Fixed some issues with Lacewing::Server on Unix:

    * SendFile didn't always complete in SSL mode
    * SendFile didn't send the file if open() returned 0
    * The Disconnect handler didn't always trigger

    