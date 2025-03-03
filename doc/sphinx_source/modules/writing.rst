Writing an Eggdrop Module
=========================

Note: This is for a simple module of 1 source file.

  1. Create a src/mod/MODULE.mod directory in your Eggdrop directory (where
     MODULE is the module name) and cd to it.

  2. Copy the file 'Makefile' from src/mod/woobie.mod and replace all
     occurrences of 'woobie' with your module name. This should ensure
     that your module gets compiled.

  3. Next, you want to create a file called MODULE.c (MODULE is the module
     name again).

  4. You MUST include the following in your source code::

      #define MODULE_NAME "module-name"

    This should be defined to the same name you will be using when you load
    your module.

    ::

      #define MAKING_MODULENAME

    MODULENAME is the name of your module (MODULE_NAME), but in all caps.

    ::

      #include "../module.h"

    This provides access to Eggdrop's global function table. Examine
    src/mod/module.h closely to find a list of functions available.

    ::

      #include any other standard c header files you might need. 

    Note that stdio.h, string.h, stdlib.h, and sys/types.h are already included.

    Note that stdio.h, string.h, stdlib.h, and sys/types.h are already included.

    ::
 
      Function *global;

    This variable provides access to all the Eggdrop functions; without it,
    you can't call any Eggdrop functions (the module won't even load).

Module requirements
-------------------

In most modules, all functions/variables (except global and MODULE_start)
should be static. This will drastically reduce the size of modules on
decent systems.

Throughout this step, MODULE refers to the module name. Note that
  "MODULE_NAME" should literally be "MODULE_NAME".

MODULE_start
^^^^^^^^^^^^
::

  char *MODULE_start(Function *func_table)

  This function is called when the module is first loaded. There are
  several things that need to be done in this function

::

  global = func_table;

  This allows you to make calls to the global function table.

::

  module_register(MODULE_NAME, MODULE_table, MAJOR, MINOR);

  This records details about the module for other modules and Eggdrop
  itself to access. MAJOR and MINOR are ints, where MAJOR is the
  module's major version number and MINOR is a minor version number.
  MODULE_table is a function table (see below).

::

  module_depend(MODULE_NAME, "another-module", MAJOR, MINOR);
  
  This lets Eggdrop know that your module NEEDS "another-module" of
  major version 'MAJOR' and at least minor version 'MINOR' to run,
  and hence should try to load it if it's not already loaded. This
  will return 1 on success, or 0 if it can't be done (at which stage
  you should return an error).

Any other initialization stuff you desire should also be included in
this function. See below for various things you can do.

You also will need to return a value. Returning NULL implies the
module loaded successfully. Returning a non-NULL STRING is an error
message. The module (and any other dependent modules) will stop
loading and an error will be returned.

MODULE_table
^^^^^^^^^^^^

::

  static Function *MODULE_table = {
         MODULE_start,
         MODULE_close,
         MODULE_expmem,
         MODULE_report,
         any_other_functions,
         you_want_to_export
  };

  This is a table of functions which any other module can access. The
  first 4 functions are FIXED. You MUST have them; they provide important
  module information.

MODULE_close ()
^^^^^^^^^^^^^^^
::

  static char *MODULE_close ()

  This is called when the module is unloaded. Apart from tidying any
  relevant data (I suggest you be thorough, we don't want any trailing
  garbage from modules), you MUST do the following:

::

  module_undepend(MODULE_NAME);

  This lets Eggdrop know your module no longer depends on any other
  modules.

  Return a value. NULL implies success; any non-NULL STRING implies
  that the module cannot be unloaded for some reason, and hence the
  bot should not unload it (see the blowfish module for an example).

MODULE_expmem
^^^^^^^^^^^^^
::

  static int MODULE_expmem ()

  This should tally all memory you allocate/deallocate within the module
  (using nmalloc, nfree, etc) in bytes. It's used by memory debugging to
  track memory faults, and it is used by .status to total up memory usage.

MODULE_report
^^^^^^^^^^^^^

::

  static void MODULE_report (int idx)
  
  This should provide a relatively short report of the module's status
  (for the module and status commands).

These functions are available to modules. MANY more available functions
can be found in src/mod/module.h.

Additional functions
^^^^^^^^^^^^^^^^^^^^

::

  void *nmalloc(int j);

  This allocates j bytes of memory.

::

  void nfree(void *a);

  This frees an nmalloc'd block of memory.

::

  Context;

  Actually a macro -- records the current position in execution (for
  debugging). Using Context is no longer recommended, because it uses
  too many resources and a core file provides much more information.

::

  void dprintf(int idx, char *format, ...)

  This acts like a normal printf() function, but it outputs to
  log/socket/idx.

  idx is a normal dcc idx, or if < 0 is a sock number.

  Other destinations:
    DP_LOG    - send to log file
    DP_STDOUT - send to stdout
    DP_MODE   - send via mode queue to the server
    DP_SERVER - send via normal queue to the server
    DP_HELP   - send via help queue to server

::

  const module_entry *module_find(char *module_name, int major, int minor);

    Searches for a loaded module (matching major, >= minor), and returns
    info about it.

    Members of module_entry:
      char *name;      - module name
      int major;       - real major version
      int minor;       - real minor version
      Function *funcs; - function table (see above)

  void module_rename(char *old_module_name, char *new_module_name)

    This renames a module frim old_module_name to new_module_name.

  void add_hook(int hook_num, Function *funcs)
  void del_hook(int hook_num, Function *funcs)

   These are used for adding or removing hooks to/from Eggdrop code that
   are triggered on various events. Valid hooks are:
     HOOK_SECONDLY   - called every second
     HOOK_MINUTELY   - called every minute
     HOOK_5MINUTELY  - called every 5 minutes
     HOOK_HOURLY     - called every hour (hourly-updates minutes past)
     HOOK_DAILY      - called when the logfiles are switched

     HOOK_READ_USERFILE - called when the userfile is read
     HOOK_USERFILE      - called when the userfile is written
     HOOK_PRE_REHASH    - called just before a rehash
     HOOK_REHASH        - called just after a rehash
     HOOK_IDLE          - called whenever the dcc connections have been
                          idle for a whole second
     HOOK_BACKUP        - called when a user/channel file backup is done
     HOOK_LOADED        - called when Eggdrop is first loaded
     HOOK_DIE           - called when Eggdrop is about to die

  char *module_unload (char *module_name);
  char *module_load (char *module_name);

    Tries to load or unload the specified module; returns 0 on success, or
    an error message.

  void add_tcl_commands(tcl_cmds *tab);
  void rem_tcl_commands(tcl_cmds *tab);

    Provides a quick way to create and remove a table of Tcl commands. The
    table is in the form of:

      {char *func_name, Function *function_to_call}

    Use { NULL, NULL } to indicate the end of the list.

  void add_tcl_ints(tcl_ints *);
  void rem_tcl_ints(tcl_ints *);

    Provides a quick way to create and remove a table of links from C
    int variables to Tcl variables (add_tcl_ints checks to see if the Tcl
    variable exists and copies it over the C one). The format of table is:

      {char *variable_name, int *variable, int readonly}

    Use {NULL, NULL, 0} to indicate the end of the list.

  void add_tcl_strings(tcl_strings *);
  void rem_tcl_strings(tcl_strings *);

    Provides a quick way to create and remove a table of links from C
    string variables to Tcl variables (add_tcl_ints checks to see if the
    Tcl variable exists and copies it over the C one). The format of table
    is:

      {char *variable_name, char *string, int length, int flags}

    Use {NULL, NULL, 0, 0} to indicate the end of the list. Use 0 for
    length if you want a const string. Use STR_DIR for flags if you want a
    '/' constantly appended; use STR_PROTECT if you want the variable set
    in the config file, but not during normal usage.

  void add_builtins(p_tcl_hash_list table, cmd_t *cc);
  void rem_builtins(p_tcl_hash_list table, cmd_t *cc);

    This adds binds to one of Eggdrop's bind tables. The format of the
    table is:

      {char *command, char *flags, Function *function, char *displayname}

    Use {NULL, NULL, NULL, NULL} to indicate the end of the list.

    This works EXACTLY like the Tcl 'bind' command. displayname is what Tcl
    sees this function's proc name as (in .binds all).

    function is called with exactly the same args as a Tcl binding is with
    type conversion taken into account (e.g. idx's are ints). Return values
    are much the same as Tcl bindings. Use int 0/1 for those which require
    0/1, or char * for those which require a string (auch as filt). Return
    nothing if no return value is required.

  void putlog (int logmode, char *channel, char *format, ...)

    Adds text to a logfile (determined by logmode and channel). This text
    will also output to any users' consoles if they have the specified
    console mode enabled.

What to do with a module?
-------------------------

   If you have written a module and feel that you wish to share it with the
   rest of the Eggdrop community, find us in #eggdrop on Libera. Make sure you
   have a nice descriptive text (modulename.desc) to describe it, and make sure
   to mention in your text file which version Eggdrop the module is written for.
