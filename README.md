<h1 align="center"> SIMPLE SHELL </h1>

This is a simple UNIX command interpreter based on bash and Sh.

## OVERVIEW

This is a sh-compatible command language interpreter that executes commands read from the standard input or from a file.

### INVOCATION

Usage: The shell is started with the standard input connected to the terminal. To start, compile all .c located in this repository by using this command: 
```
gcc -Wall -Werror -Wextra -pedantic -std=gnu89 *.c -o shell
./shell
```

**Shell** is allowed to be invoked interactively and non-interactively. If **shell** is invoked with standard input not connected to a terminal, it reads and executes received commands in order.

Example:
```
$ echo "echo 'Alx School'" | ./shell
'ALX SCHOOL'
$
```

When **shell** is invoked with standard input connected to a terminal (determined by isatty(3), the interactive mode is opened. **shell** Will be using the following prompt `^-^ `.

Example:
```
$./shell
^-^
```

If a command line argument is invoked, **shell** will take that first argument as a file from which to read commands.

Example:
```
$ cat text
echo 'Alx School'
$ ./shell text
'ALX SCHOOL'
$
```

### Environment

Upon invocation, **shell** receives and copies the environment of the parent process in which it was executed. This environment is an array of *name-value* strings describing variables in the format *NAME=VALUE*. A few key environmental variables are:

#### HOME
The home directory of the current user and the default directory argument for the **cd** builtin command.

```
$ echo "echo $HOME" | ./shell
/home/vagrant
```

#### PWD
The current working directory as set by the **cd** command.

```
$ echo "echo $PWD" | ./shell
/home/vagrant/ALX/simple_shell
```

#### OLDPWD
The previous working directory as set by the **cd** command.

```
$ echo "echo $OLDPWD" | ./shell
/home/vagrant/ALX/bog-062019-test_suite
```

#### PATH
A colon-separated list of directories in which the shell looks for commands. A null directory name in the path (represented by any of two adjacent colons, an initial colon, or a trailing colon) indicates the current directory.

```
$ echo "echo $PATH" | ./shell
/home/vagrant/.cargo/bin:/home/vagrant/.local/bin:/home/vagrant/.rbenv/plugins/ruby-build/bin:/home/vagrant/.rbenv/shims:/home/vagrant/.rbenv/bin:/home/vagrant/.nvm/versions/node/v10.15.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/vagrant/.cargo/bin:/home/vagrant/workflow:/home/vagrant/.local/bin
```

### Command Execution

After receiving a command, **shell** tokenizes it into words using `" "` as a delimiter. The first word is considered the command and all remaining words are considered arguments to that command. **shell** then proceeds with the following actions:
1. If the first character of the command is neither a slash (`\`) nor dot (`.`), the shell searches for it in the list of shell builtins. If there exists a builtin by that name, the builtin is invoked.
2. If the first character of the command is none of a slash (`\`), dot (`.`), nor builtin, **shell** searches each element of the **PATH** environmental variable for a directory containing an executable file by that name.
3. If the first character of the command is a slash (`\`) or dot (`.`) or either of the above searches was successful, the shell executes the named program with any remaining given arguments in a separate execution environment.

### Exit Status 

**shell** returns the exit status of the last command executed, with zero indicating success and non-zero indicating failure.
If a command is not found, the return status is 127; if a command is found but is not executable, the return status is 126.
All builtins return zero on success and one or two on incorrect usage (indicated by a corresponding error message).

### Signals

While running in interactive mode, **shell** ignores the keyboard input ctrl+c. Alternatively, an input of End-Of-File ctrl+d will exit the program.

User hits ctrl+d in the foutrh command.
```
$ ./shell
^-^ ^C
^-^ ^C
^-^ ^C
^-^
```

### Variable Replacement

**shell** interprets the `$` character for variable replacement.

#### $ENV_VARIABLE
`ENV_VARIABLE` is substituted with its value.

Example:
```
$ echo "echo $PWD" | ./shell
/home/vagrant/holberton/simple_shell
```

#### $?
`?` is substitued with the return value of the last program executed.

Example:
```
$ echo "echo $?" | ./shell
0
```

#### $$
The second `$` is substitued with the current process ID.

Example:
```
$ echo "echo $$" | ./shell
3855
```

### Comments

**shell** ignores all words and characters preceeded by a `#` character on a line.

Example:
```
$ echo "echo 'ALX' #this will be ignored!" | ./shell
'ALX'
```

### Operators

**shell** specially interprets the following operator characters:

#### ; - Command separator
Commands separated by a `;` are executed sequentially.

Example:
```
$ echo "echo 'hello' ; echo 'world'" | ./shell
'hello'
'world'
```

#### && - AND logical operator
`command1 && command2`: `command2` is executed if, and only if, `command1` returns an exit status of zero.

Example:
```
$ echo "error! && echo 'ALX'" | ./shell
./shellby: 1: error!: not found
$ echo "echo 'my name is' && echo 'ALX'" | ./shell
'my name is'
'ALX'
```

#### || - OR logical operator
`command1 || command2`: `command2` is executed if, and only if, `command1` returns a non-zero exit status.

Example:
```
$ echo "error! || echo 'wait for it'" | ./shell
./shell: 1: error!: not found
'wait for it'
```

The operators `&&` and `||` have equal precedence, followed by `;`.

### Builtin Commands

#### cd
  * Usage: `cd [DIRECTORY]`
  * Changes the current directory of the process to `DIRECTORY`.
  * If no argument is given, the command is interpreted as `cd $HOME`.
  * If the argument `-` is given, the command is interpreted as `cd $OLDPWD` and the pathname of the new working directory is printed to standad output.
  * If the argument, `--` is given, the command is interpreted as `cd $OLDPWD` but the pathname of the new working directory is not printed.
  * The environment variables `PWD` and `OLDPWD` are updated after a change of directory.

Example:
```
$ ./shell
^-^ pwd
/home/vagrant/ALX/simple_shell
$ cd ../
^-^ pwd
/home/vagrant/ALX
^-^ cd -
^-^ pwd
/home/vagrant/ALX/simple_shell
```

#### exit
  * Usage: `exit [STATUS]`
  * Exits the shell.
  * The `STATUS` argument is the integer used to exit the shell.
  * If no argument is given, the command is interpreted as `exit 0`.

Example:
```
$ ./shell
$ exit
```

#### env
  * Usage: `env`
  * Prints the current environment.

Example:
```
$ ./shell
$ env
NVM_DIR=/home/vagrant/.nvm
...
```

#### setenv
  * Usage: `setenv [VARIABLE] [VALUE]`
  * Initializes a new environment variable, or modifies an existing one.
  * Upon failure, prints a message to `stderr`.

Example:
```
$ ./shell
$ setenv NAME Alx
$ echo $NAME
ALX
```

#### unsetenv
  * Usage: `unsetenv [VARIABLE]`
  * Removes an environmental variable.
  * Upon failure, prints a message to `stderr`.

Example:
```
$ ./shell
$ setenv NAME Alx
$ unsetenv NAME
$ echo $NAME

$
```

## Authors & Copyrights

* Ifeoluwa Ibitowa
* Abdulmutalib Abdulsalam
