crashlog.py
===========

What is this?
-------------

LLDB_ comes with a Python package, ``lldb.macosx.crashlog`` that can be used
to symbolicate Mac OS X crash logs.  You're supposed to use it like this::

  $ lldb
  (lldb) command script import lldb.macosx.crashlog
  "crashlog" and "save_crashlog" command installed, use the "--help" option for detailed help
  "malloc_info", "ptr_refs", "cstr_refs", and "objc_refs" commands have been installed, use the "--help" options on these commands for detailed help.
  (lldb) crashlog /path/to/crash.log

Unlike other symbolicators, it annotates the backtrace with your actual source
code (and/or in some cases disassembly) so that you can see where the crash
took place.  Additionally, if you run it as above, within LLDB itself, it will
set up the memory map as if your program was loaded.  The other option is to
invoke it directly; if you are using the version within Xcode, it's in
``Xcode.app/Contents/SharedFrameworks/LLDB.framework/Versions/A/Resources/Python/lldb/macosx/crashlog.py``,
which makes for rather a lot of typing!

Unfortunately the version shipped with LLDB and Xcode 7 doesn't quite work
properly; not only does it rely on a script, ``dsymForUUID``, that is not
provided and whose behaviour is not documented anywhere, but it chokes on some
crash logs, and the neat memory map feature only loads the ``__TEXT`` segment
in the correct place, which makes it somewhat less useful than it might
otherwise be.

So, really, what is this?
-------------------------

It's a fixed version of ``crashlog.py``, with the following additional
features:

1. It doesn't need ``dsymForUUID``.  In fact, it's faster without it!  But,
   if you work at Apple and want to use the version you have there, you can
   do so by setting the ``DSYMFORUUID`` environment variable to the full path
   of the script before running it.

2. It can also process the output from the ``sample`` tool (or, equivalently,
   the Sample button in Activity Monitor).

How do I use it?
----------------

I recommend installing the ``crashlog.py`` script in ``/usr/local/bin``::

  sudo cp crashlog.py /usr/local/bin/crashlog
  sudo chmod ugo+x /usr/local/bin/crashlog

That way you can add ``/usr/local/bin`` to your ``PATH`` (if it isn't
already there) and you can just invoke it directly, e.g.::

  $ crashlog /path/to/crash.log

.. _LLDB: http://lldb.llvm.org
