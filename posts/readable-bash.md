# Readable Bash

This is a compilation of simple bash style guidelines that I use to make cli scripts that I actually like to use and scale. This document does not offer implementation details.

## Every function accessible through the cli gets a help function

if I have a script- let's say something silly around sending an email via curl- I will probably structure call it `mail`. This script will have a `mail help` or `mail --help` functionality exposed. Two functions of `mail` might be `compose` and `send`. These two sub functions must also have a `help` or `--help` flag exposed.

## Help functions describe the current function in detail, and only name sub functions.

Nobody likes to read a surprise textbook, and documentation should read like a spiders web, and not spaghetti. In the `mail` example above, the `mail help` command should list the types of flags, options, and commands accessible to the `mail` script, but say nothing about `mail send` other than to indicate it exists.

That being said. each help function should follow some basic rules, which brings us to our next rule:

## every help function should follow the same pattern: info, usage, examples.

People will not be able to Google your scripts and find tons of info on how to run it.  These are scripts that you'll likely never use again or that will become a core part of the software infrastructure. Nobody could predict which, but regardless, describing the expectations of the user is always a good practice.  Additionally, breaking it down like that allows us to automatically build our our documentation as we code.

## Always write and install and removal script

During development you will need to install to a machine and remove several times. Ensuring it can be seemlessly installed and removed it vital to proper functioning. when pushing to a package manager, an install script makes it vastly simpler.

## Every function that has a help function also has an info function exposed to the user, even if it's not documented.

This is the top one-line description of what the function does.  In our example, `menu info` would say echo something to the console like "sends mail using curl from the command line".  `menu help` would not list `info` as a function, but `menu help` would print to the console something like:

```
$ menu help
sends mail using curl from the command line

mail [send|compose|help]

send       sends an email 
compose    composes an email
```

Notice the first line has the same information as `menu info` would describe, but `info` is not in the list of described functions. `info` can be part of the next higher level's help output 

## Prepend functions exposed to the user with the name of the script, and then leverage that regex to automatically build your documentation. 

Encapsulating the sending functionality in a function called `menu_send` which is exposed to the user, we can automatically add the send function to the `menu help` output by parsing the names of the functions, creating a list of the functions starting with `menu_`, and calling _that_ function's `info` function.  This allows the code to become automatically documented.