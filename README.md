# Egg Shell

A simple UNIX command line interpreter, written in C as part of the Holberton School programming track. This project was an opportunity to learn about how a shell like `sh` actually works under the hood — reading input, splitting it into commands, searching the enviornment for an executable in `PATH`, and `fork`/`exec`-ing it.

## Description

`hsh` reads a line of input, breaks it into tokens, searches for the command against the directories listed in `PATH` (or uses it directly if it already looks like a path), and then forks a child process to run it with `execve`. It works both interactively, where you get a prompt and type commands one at a time, and non-interactively, where you can pipe commands into it from a file or another program.

## Compilation

```
git clone https://github.com/papadar/holbertonschool-simple_shell.git
cd holbertonschool-simple_shell
gcc -Wall -Werror -Wextra -pedantic -std=gnu89 *.c -o hsh
```

That results in an executable named `hsh` in the current directory.

## Usage

RTFM - man_1_simple_shell - invoke from the directory;

```
$  man ./man_1_simple_shell
 ```

### Interactive mode

Run the binary, You'll see a `$$$ ` prompt after every command:

```
$ ./hsh
$$$ /bin/ls
AUTHORS    env.c       exit.c            input.c     main.c              path.c          shell.h           string_array.c
README.md  executor.c  handle_command.c  internal.c  man_1_simple_shell  print_prompt.c  signal_handler.c  tokens.c
$$$ exit
```

### Non-interactive mode

You can also pass commands from a file or another process, no prompt will be printed and it will handle the cammond if it is able to do so:

```
$ echo "/bin/ls" | ./hsh
AUTHORS    env.c       exit.c            input.c     main.c              path.c          shell.h           string_array.c
README.md  executor.c  handle_command.c  internal.c  man_1_simple_shell  print_prompt.c  signal_handler.c  tokens.c
```
```
$ cat commands.txt | ./hsh
```

## How we find commands

If the received string contains a `/` (like `/bin/ls` or `./hsh`), the shell tries to run it as-is. Otherwise it searches each directory listed in the `PATH` environment variable, adding the command to each location, looking for a matching binary. If nothing is found, you get an error that mimics `sh`'s own format:

```
$$$ egg_shell
./hsh: 1: egg_shell: not found
```

## Signal handling

Hitting `Ctrl+C` (`SIGINT`) while sitting at the prompt won't kill the shell — rather it prints a newline and gives you a fresh prompt, same as a real shell. It only affects the shell itself while it's waiting for input; once a command is actually running, that command handles its own signals.

## Builtins
We have implemented two builtin commands;

* `env` - The env command displays each of the environment variables with their values.

* `exit` - with 'Ctrl+C' being handled, typing exit allows the user to end the process.

## Files

| File | What it does |
| --- | --- |
| `AUTHORS` | List of everyone who's contributed |
| `README.md` | This Readme File |
| `env.c` | prints each value found in the environment passed to the shell |
| `executor.c` | Forks and runs a command with `execve` |
| `exit.c` | free's all malloc'd memory, and passes the exit status |
| `handle_command.c` | compares received command with built-ins to decide operation |
| `input.c` | Reads one line of input from the user or from a pipe |
| `internal.c` | compared commands against strings to detect in buit-ins |
| `main.c` | Entry point, runs the main read-tokenize-execute loop |
| `man_1_simple_shell` | Man page for the shell |
| `path.c` | Resolves a command name to a full path using `PATH` |
| `print_prompt.c` | Prints the `$$$ ` prompt in interactive mode |
| `shell.h` | Header file — all the function prototypes and includes live here |
| `signal_handler.c` | handles Ctrl+C (SIGINT) while the shell is waiting for input |
| `string_array.c` | builds a malloc'd array for storing strings & cmd's |
| `tokens.c` | Splits a line into tokens, by spaces or by pipes |


## Known limitations

This project is very much a first pass that has been shaped by the testing criteria, so a few things `sh` normally does aren't there yet:

- Missing built-in commands such as `cd`, `pwd`, `alias`.
- No Command history or previous command editing implemented.
- No support for logical operators (`&&`, `||`), redirection (`>`, `<`), or variable expansion (`$HOME`, `$?`, etc).
- Tokenizing does split on the pipe character `|`, laying some groundwork, but piping commands into each other isn't hooked up yet.

## Testing

Valgrind testing shows the 1.0.0 build has zero errors after running some commands and then exiting.
```
==632== HEAP SUMMARY:
==632==     in use at exit: 0 bytes in 0 blocks
==632==   total heap usage: 13 allocs, 13 frees, 2,513 bytes allocated
==632== All heap blocks were freed -- no leaks are possible
==632== For lists of detected and suppressed errors, rerun with: -s
==632== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

## Authors
* Aladar Apponyi - https://github.com/papadar
* Lucas Fisher - https://github.com/14808-Lucas
