Last revised: February 12, 2002

.. _ctcp:

===========
CTCP Module
===========

This module provides the normal ctcp replies that you would expect.
Without it loaded, CTCP CHAT will not work.

This module requires: server

Put this line into your Eggdrop configuration file to load the ctcp
module::

  loadmodule ctcp

There are also some variables you can set in your config file:

  set ctcp-mode 0
    Set here how the ctcp module should answer ctcps. There are 3 possible
    operating modes:

    +---+--------------------------------------------------------------+
    | 0 | Normal behavior is used.                                     |
    +---+--------------------------------------------------------------+
    | 1 | The bot ignores all ctcps, except for CHAT and PING requests |
    |   | by users with the +o flag.                                   |
    +---+--------------------------------------------------------------+
    | 2 | Normal behavior is used, however the bot will not answer more|
    |   | than X ctcps in Y seconds (defined by 'set flood-ctcp').     |
    +---+--------------------------------------------------------------+

There are also several variables to help make your bot less noticeable.
They are: ctcp-version, ctcp-finger, and ctcp-userinfo. You can use set
to set them to values you'd like.


Copyright (C) 2000 - 2022 Eggheads Development Team
