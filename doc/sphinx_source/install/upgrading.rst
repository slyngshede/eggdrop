Upgrading Eggdrop
=================

  It is easy to upgrade Eggdrop to a new version! To have a full picture of the changes made since your last upgrade, we recommend reading the NEWS file. Upgrades from the 1.6 and 1.8 lines of Eggdrop should take place with little to no issues. The config file, user files, and channel files can all be reused.

  For support, feel free to visit us on Libera #eggdrop.

How to Upgrade
--------------

  To upgrade to a newer version of Eggdrop, first backup your .config file, .user file, and .chan file. While they are saved and should not be overwritten by the upgrade process, it never hurts to make anothere copy :)

  Next, view the NEWS file to see what changes have been made, paying particular attention to the config file section to see what new/different commands have been added. Update your config file appropriately.

  Then, simply follow the same steps you followed to install Eggdrop previously. Download the source, unzip/untar it, and run the './configure', 'make config', 'make', and 'make install' commands. Restart your Eggdrop and you will be up and running with the latest version of Eggdrop.

Must-read changes for Eggdrop v1.9
----------------------------------

These are NOT all the changes or new settings; rather just the "killer" changes that may directly affect Eggdrop's previous performance without modification.

Config file changes
^^^^^^^^^^^^^^^^^^^

To migrate from a 1.8 to a 1.9 Eggdrop, some changes are suggested to be made in your configuration file:

* Eggdrop has deprecated the blowfish module for password hashing in favor of the PBKDF2 module. This is a BIG change which, if done carelessly, has the potential to render stored passwords useless. Please see doc/PBKDF2 for information on how to properly migrate your userfiles and passwords to the new module.

* Eggdrop 1.9 switched from the "set servers {}" syntax to the "server add" command. For example, if your configuration file previously had::

    set servers {
      my.server.com:6667
    }

  you should now instead use::

    server add my.server.com 6667

  Please read the config file for additional examples

* Eggdrop no longer requires the '-n' flag to start Eggdrop in terminal mode.


Modules
^^^^^^^

While most 3rd party modules that worked on Eggdrop v1.6/v1.8 should still work with Eggdrop v1.9, many of them contain a version check that only allows them to run on 1.6.x bots. We have removed the version check from some of the more popular modules provide them at `<ftp://eggheads.org/pub/eggdrop/modules/1.9/>`_

Scripts
^^^^^^^

All 3rd party Tcl scripts that work with Eggdrop v1.6/v1.8 should fully work with Eggdrop v1.9.

Botnet
^^^^^^

In Eggdrop v1.8, Eggdrop bots would automatically attempt to upgrade any botnet link to an SSL/TLS connection. In v1.9, the user is required to explicitly request an SSL/TLS connection by prefixing the port with a '+'. If you wish your botnet to take advantage of encryption, use the .chaddr command to update your ports to start with a '+'.

Tcl Commands
^^^^^^^^^^^^

A lot of backwards-compatible additions and changes have been made to Tcl commands. Please look at doc/tcl-commands.doc to see them.

Documentation
^^^^^^^^^^^^^

Documentation has been updated to reflect new and removed commands and variables.
