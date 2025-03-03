Last revised: December 31, 2001

==============
The Party Line
==============

The most important way you will communicate with your bot is through
the party line. The party line is accessible via DCC chat or telnet.
It's pretty much just a miniature, lag-less IRC (see doc/BOTNET), but
it also consists of a console through which you can watch channel
activity and give commands.

To enter the party line, DCC chat or open a telnet connection to your
bot. It should ask for your password if you've set one. If you don't
have one set, use the /msg <bot> PASS <new password> command to set one.

The party line is actually split up into 200,000 "channels". The console
is available from each channel, but you can only talk to people who are
on your current channel (just like IRC). Channel 0 is the main party
line, while others are typically reserved for private conversations.
Channels 1-99,999 are botnet wide chat channels and any user joining
that channel anywhere on the botnet will be able to chat with you.
Channels \*0-\*99,999 are local channels (only people on the bot you
are on can chat with you on these channels).

Console commands start with a dot (.), similar to the slash (/) used
for IRC commands. At any time, you can type ".help all" to get a list
of all possible commands. To find out what a command does, use ".help
<command>". For example: ".help channel".

When you're on the party line, anything you type that doesn't start with
a dot (.), a comma (,), or an apostrophe (') is considered to be
broadcast to everyone else, just like talking on a channel. A message
prefixed with a comma goes only to other bot owners (+n). A message
prefixed with an apostrophe is sent to all users on the local bot only.
You can change channels with the ".chat" command or even leave all
channels with ".chat off".

Copyright (C) 2002 - 2022 Eggheads Development Team
