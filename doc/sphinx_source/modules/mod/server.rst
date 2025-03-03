Last revised: October 25, 2010

.. _server:

=============
Server Module
=============

This module provides the core server support. You have to load this if
you want your bot to come on IRC. Not loading this is equivalent to the
old NO_IRC define.

This module requires: none

Put this line into your Eggdrop configuration file to load the server
module::

  loadmodule server

There are also some variables you can set in your config file:

  set net-type Efnet
    What is your network? Possible allowed values are Efnet, IRCnet, Undernet,
    DALnet, Libera, freenode, Quakenet, Rizon, Other. If the network you use is
    not listed, using "Other" is a good sane choice and can be customized with
    settings both here and in the IRC module sections of the config file.

  set nick "LamestBot"
    Set the nick the bot uses on IRC, and on the botnet unless you specify a
    separate botnet-nick, here.

  set altnick "L?m?stB?t"
    Set the alternative nick which the bot uses on IRC if the nick specified
    by 'set nick' is unavailable. All '?' characters will be replaced by
    random numbers.

  set keep-nick 1
    This setting makes the bot try to get his original nickname back if its
    primary nickname is already in use.

  set realname "/msg LamestBot hello"
    Set here what to display in the real-name field for the bot.

  ::

    bind evnt - init-server evnt:init_server
    proc evnt:init_server {type} {
      global botnick
      putquick "MODE $botnick +i-ws"
    }

  ... is Tcl scripting that is run immediately after connecting to a server.

  ::

    set init-server { putquick "MODE $botnick +i-ws" }

  ... is Tcl scripting that is run immediately after connecting to a server.
  It is limited to 120 characters, and is depriciated due to the EVNT bind
  (see doc/tcl-commands.doc).

  ::

    set connect-server { putlog "Connecting to server." }

  ... is Tcl scripting that is run immediately before connecting to a server.
  It is limited to 120 characters, and is depriciated due to the EVNT bind
  (see doc/tcl-commands.doc).

  ::

    set disconnect-server { putlog "Disconnected from server." }

  ... is a Tcl script that is run immediately after disconnecting from a
  server. It is limited to 120 characters, and is depriciated due to the
  EVNT bind (see doc/tcl-commands.doc).

  ::

    set servers {
      you.need.to.change.this:6667
      another.example.com:7000:password
      [2001:db8:618:5c0:263::]:6669:password
      ssl.example.net:+6697
    }

  ... is the bot's server list. The bot will start at the first server
  listed, and cycle through them whenever it gets disconnected. You need
  to change these servers to YOUR network's servers.

    The format is:
      server[:port[:password]]
    Prefix the port with a plus sign to attempt a SSL connection:
      server:+port[:password]

    Both the port and password fields are optional; however, if you want to
    set a password or use SSL you must also set a port. If a port isn't
    specified it will default to your default-port setting.

  set default-port 6667
    Set the default port which should be used if none is specified with
    '.jump' or in 'set servers'.

  set msg-rate 2
    Number of seconds to wait between transmitting queued lines to the
    server. Lower this value at your own risk.  ircd is known to start
    flood control at 512 bytes/2 seconds.

  set ssl-verify-servers 0
    Control certificate verification for servers. You can set this by adding
    together the numbers for all exceptions you want to enable. By default
    certificate verification is disabled and all certificates are assumed to
    be valid. The numbers are the following:
      
    The numbers are the following:

      +---+---------------------------------------------+
      | 0 | disable verification                        |
      +---+---------------------------------------------+
      | 1 | enable certificate verification             |
      +---+---------------------------------------------+
      | 2 | allow self-signed certificates              |
      +---+---------------------------------------------+
      | 4 | don't check peer common or alt names        |
      +---+---------------------------------------------+
      | 8 | allow expired certificates                  |
      +---+---------------------------------------------+
      | 16| allow certificates which are not valid yet  |
      +---+---------------------------------------------+
      | 32| allow revoked certificates                  |
      +---+---------------------------------------------+

  set server-cycle-wait 60
    This setting defines how long Eggdrop should wait before moving from one
    server to another on disconnect. If you set 0 here, Eggdrop will not wait
    at all and will connect instantly. Setting this too low could result in
    your bot being K:Lined.

  set server-timeout 60
    Set here how long Eggdrop should wait for a response when connecting to
    a server before giving up and moving on to next server.

  set check-stoned 1
    Set this to 1 if Eggdrop should check for stoned servers? (where the
    server connection has died, but Eggdrop hasn't been notified yet).

  set serverror-quit 1
    If you want your bot to exit the server if it receives an ERROR message,
    set this to 1.

  set max-queue-msg 300
    Set here the maximum number of lines to queue to the server. If you're
    going to dump large chunks of text to people over IRC, you will probably
    want to raise this. 300 is fine for most people though.

  set quiet-reject 1
    This setting makes the bot squelch the error message when rejecting a
    DCC CHAT, SEND or message command. Normally, Eggdrop notifies the user
    that the command has been rejected because they don't have access. Note
    that sometimes IRC server operators detect bots that way.

  set flood-msg 5:60
    Set here how many msgs in how many seconds from one host constitutes
    a flood. If you set this to 0:0, msg flood protection will be disabled.

  set flood-ctcp 3:60
    Set here how many ctcps in how many seconds from one host are
    recognized as a flood. Not specifying or using number = 0
    will not check against ctcp floods.

  set answer-ctcp 3
    Set how many ctcps should be answered at once here.

  set lowercase-ctcp 0
    If you want your bot to answer lower case ctcp requests (non rfc-
    compliant), set this setting to 1. mIRC will do this, most other
    clients will not.

  set trigger-on-ignore 0
    If you want Eggdrop to trigger binds for ignored users, set this to 1.

  set exclusive-binds 0
    This setting configures PUBM and MSGM binds to be exclusive of PUB
    and MSG binds. This means if a MSGM bind with the mask "*help*" exists
    and is triggered, any MSG bindings with "help" in their mask will not
    be triggered. Don't enable this unless you know what you are doing!

  set double-mode 0
    Allow identical messages in the mode queue?

  set double-server 0
    Allow identical messages in the server queue?

  set double-help 0
    Allow identical messages in the help queue?

  set use-penalties 1 (default on net-type IRCnet)
    This enables Eggdrop's penalty calculation. Every command Eggdrop sends
    to the IRC server raises its penalty points. If Eggdrop reaches a server
    limit, it gets disconnected with "excess flood" message. Eggdrop is able
    to count internal those penalty points, too and take measures against
    excess flooding. Note: it's highly advised to turn this on!

  set optimize-kicks 1
    This optimizes the kick queue. It also traces nick changes and parts in
    the channel and changes the kick queue accordingly. There are three
    different options for this setting:

      +---+------------------------------------------------------------------+
      | 0 | Turn it off.                                                     |
      +---+------------------------------------------------------------------+
      | 1 | Optimize the kick queue by summarizing kicks.                    |
      +---+------------------------------------------------------------------+
      | 2 | Trace nick changes and parts on the channel and change the queue |
      |   | accordingly. For example, bot will not try to kick users who have|
      |   | already parted the channel.                                      |
      +---+------------------------------------------------------------------+

    ATTENTION: Setting 2 is very CPU intensive.

There are additional settings for 'net-type' Efnet.

  *net-type 5 specific features:*

  Attention: Use this settings *only* if you set 'net-type' to Efnet!

    set check-mode-r 1
      This settings defines how umode +r is understood by Eggdrop. Some
      networks use +r to indicate a restricted connection. If this is your
      case, and you want your bot to leave restricted servers and jump to
      the next server on its list, then set it to 1.
      Please note, this setting is automatically set to 0 for net-type of
      Efnet, Undernet, and DALnet, and set to 1 for net-type IRCNet.

    set nick-len 9
      This setting allows you to specify the maximum nick-length supported by
      your network. The default setting is 9. The maximum supported length by
      Eggdrop is 32.

  Copyright (C) 2000 - 2022 Eggheads Development Team

