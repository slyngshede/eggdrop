Last revised: May 27, 2004

.. _compress:

===============
Compress Module
===============

This module provides support for file compression. It can be used
to compress files via Tcl or to transfer the userfile compressed during the
share process, saving bandwidth.

This module requires: share

Put this line into your Eggdrop configuration file to load the compress
module::

  loadmodule compress

There are also some variables you can set in your config file:

  set share-compressed 1
    Allow compressed sending of user files? The user files are compressed
    with the compression level defined in 'compress-level'.

  set compress-level 9
    This is the default compression level used. These levels are the same
    as those used by GNU gzip.


Copyright (C) 2000 - 2022 Eggheads Development Team
