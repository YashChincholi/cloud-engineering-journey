# Day 01: Working with the Shell - I

## Overview

Day 01 of my **Cloud Engineering Journey** focuses on Linux shell fundamentals and hands-on command-line practice.

I practiced these concepts in my own Linux environment and documented the work with screenshots captured from my local terminal.

### Topics Covered

- Terminal session recording with `script`
- Command identification with `type` and `which`
- Working with the current and home directories
- `pwd`, `cd`, `pushd`, and `popd`
- Creating nested directories with `mkdir -p`
- Creating files with `touch`
- Moving and renaming with `mv`
- Copying with `cp`
- Removing files/directories with `rm`
- Viewing file contents with `cat`, `more`, and `less`
- Date formatting with `date`
- Command documentation with `--help` and `apropos`
- Command aliases with `alias` and `unalias`
- Command history with `history`
- Environment variables with `echo`, `printenv`, and `env`
- Modifying `PATH` with `export`
- Locating executables with `which`
- Customizing the shell prompt with `PS1`

> **Note:** The screenshots in this directory are my own local terminal/session screenshots. Screenshots from the training platform used while learning the material have been excluded from this public repository.

---

## Directory & File Structure Practiced

The hands-on exercises created and manipulated the following filesystem structure:

```text
day-01_Working with the shell - I/
├── Africa/
│   ├── Egypt/
│   │   └── Cairo/
│   │       └── City.txt
│   └── Morocco/
├── America/
│   └── USA/
├── Asia/
│   ├── China/
│   │   └── Country.txt
│   └── India/
│       └── Mumbai/
│           └── City.txt
├── Europe/
│   └── UK/
│       └── London/
├── images/
└── terminal-session.txt
```

---

## 1. Terminal Session Recording

I used `script` to record terminal input and output into a text file.

```bash
script terminal-session.txt
```

To finish the recording:

```bash
exit
```

This provides a plain-text record of the commands and output from the local practice session.

---

## 2. Identifying Commands

The `type` command can be used to identify how a command is provided by the shell.

```bash
type echo
type mv
type uptime
```

I also used `which` to locate executable files:

```bash
which obs-studio
```

### Why this matters

Understanding whether something is a shell builtin, alias, or external executable is useful when troubleshooting command behavior and the shell environment.

---

## 3. Navigation & Directory Operations

### Check the current directory

```bash
pwd
```

### Move to the home directory

```bash
cd ~
```

### Create nested directories

```bash
mkdir -p Asia/India/Mumbai
mkdir -p Europe/UK/London
mkdir -p Africa/Egypt/Cairo
```

The `-p` option allows parent directories to be created when they do not already exist.

### Directory stack

I also practiced `pushd` and `popd`:

```bash
pushd /home/yash/projects/cloud-engineering-journey/
popd
```

These commands are useful when temporarily moving between directories while keeping track of the previous location.

---

## 4. File Creation & File Operations

### Create a file

```bash
touch Asia/India/Mumbai/City.txt
```

### Copy a file

```bash
cp Asia/India/Mumbai/City.txt Africa/Egypt/Cairo/
```

### Move or rename

```bash
mv source destination
```

For example, moving a directory to another location:

```bash
mv Europe/Morocco/ Africa/Morocco
```

### Remove a file

```bash
rm Europe/UK/London/Tottenham.txt
```

> `rm` permanently removes files/directories from the filesystem, so paths should be checked carefully before running it.

---

## 5. Viewing File Contents

I practiced several commands for inspecting text files:

```bash
cat Africa/Egypt/Cairo/City.txt
more Africa/Egypt/Cairo/City.txt
less Africa/Egypt/Cairo/City.txt
```

I also used:

```bash
ls -la
```

to inspect directory contents, including hidden files and detailed file information.

---

## 6. Date Formatting & Command Help

### Date formatting

I practiced formatting the output of `date`:

```bash
date +"%Y-%m-%d %H:%M:%S"
```

### Built-in help

```bash
date --help
```

This provides command-specific usage information and available options.

### Search command documentation

I also practiced:

```bash
apropos modpr
```

`apropos` searches available manual-page descriptions for matching keywords.

---

## 7. Aliases & Command History

### Create a temporary alias

```bash
alias dt=date
```

The alias can then be used as a shortcut for the command.

### Remove the alias

```bash
unalias dt
```

### View command history

```bash
history
```

Command history is useful for reviewing previously executed commands and repeating earlier work.

---

## 8. Environment Variables

I inspected the current shell and environment:

```bash
echo $SHELL
env
```

I also practiced:

```bash
printenv
printenv OFFICE
echo $LOGNAME
```

Environment variables provide configuration and contextual information to processes running in the shell.

---

## 9. PATH Modification

The `PATH` variable controls the directories searched by the shell when looking for executable commands.

Check the current value:

```bash
echo $PATH
```

Append a directory:

```bash
export PATH=$PATH:/opt/obs/bin
```

Then verify executable resolution with:

```bash
which obs-studio
```

This helped me understand the relationship between `PATH`, executable locations, and command lookup.

---

## 10. Shell Prompt Customization

I practiced changing the shell prompt using `PS1`:

```bash
PS1="[\d \t \u@\h:\w ] $ "
```

This changes the appearance of the interactive shell prompt.

The prompt can include information such as:

- Date
- Time
- Username
- Hostname
- Current working directory

---

## 11. Evidence From My Local Terminal

The following screenshots are the public evidence included with this Day 01 documentation.

| # | Screenshot | What It Shows |
|---|---|---|
| 01 | `01_command_types_and_help.png` | Command classification and help commands |
| 02 | `02_directory_structure_tree.png` | The filesystem structure created during practice |
| 03 | `03_man_page_date.png` | Reading the `date` manual page |
| 04 | `04_environment_variables.png` | Environment-variable practice |
| 05 | `05_terminal_navigation_and_directory_creation.png` | Session recording, navigation, `type`, and `mkdir -p` |
| 06 | `06_file_operations_and_viewing.png` | `touch`, `mv`, `cp`, `rm`, `cat`, `more`, `less`, and `ls -la` |
| 07 | `07_date_formatting_and_help_flags.png` | `date` formatting and `date --help` |
| 08 | `08_apropos_aliases_and_command_history.png` | `apropos`, aliases, `unalias`, and `history` |
| 09 | `09_inspecting_environment_variables.png` | `$SHELL`, `env`, and environment inspection |
| 10 | `10_path_modification_and_prompt_customization.png.png` | `PATH`, `which`, `PS1`, and terminal-session metadata |

---

## 12. Repository Structure

Current Day 01 structure:

```text
linux/
└── day-01_Working with the shell - I/
    ├── Africa/
    │   ├── Egypt/
    │   │   └── Cairo/
    │   │       └── City.txt
    │   └── Morocco/
    ├── America/
    │   └── USA/
    ├── Asia/
    │   ├── China/
    │   │   └── Country.txt
    │   └── India/
    │       └── Mumbai/
    │           └── City.txt
    ├── Europe/
    │   └── UK/
    │       └── London/
    ├── images/
    └── terminal-session.txt
```

The `images/` directory contains only locally captured evidence.

---

## 13. Key Takeaways

### Commands I practiced

```text
script
type
which
pwd
cd
pushd
popd
mkdir
touch
mv
cp
rm
cat
more
less
ls
date
apropos
alias
unalias
history
echo
env
printenv
export
```

### Concepts I understood better

- How the Linux shell identifies commands
- Navigating the filesystem efficiently
- Creating and manipulating directories and files
- Reading command documentation
- Using aliases and command history
- Understanding environment variables
- How `PATH` affects executable lookup
- Customizing the interactive shell prompt
- Recording and documenting terminal work

---

## 14. Learning Approach

The goal of this journey is not just to memorize commands.

My approach is:

```text
Learn
  ↓
Practice
  ↓
Build
  ↓
Break
  ↓
Troubleshoot
  ↓
Document
  ↓
Repeat
```

This Day 01 lab establishes the Linux command-line foundation for the next stages of my Cloud Engineering Journey.

---

## 15. Next Steps

Continue building Linux fundamentals alongside the Networking track.

Planned Linux topics include:

- Filesystem fundamentals
- Permissions
- Users & groups
- Processes
- Services
- Package management
- SSH
- Linux networking commands
- Logs
- Storage
- Bash scripting
- Troubleshooting

