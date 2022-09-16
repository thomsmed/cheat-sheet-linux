# Bash Commands

|Command|Description|
|---|---|
|**System information**||
|`uname -a`|Print system information|
|`lscpu`|Display information about the CPU architecture|
|`lsblk`|List block devices|
|`lsusb -v`|List USB devices|
|`getconf -a`|Query system configuration variables|
|`arch`|Print machine architecture|
|**Process management**||
|`ps`|Snapshot of the current processes|
|`top`|Realtime view of current processes|
|`vmstat`|Report virtual memory statistics|
|**Directories**||
|`pwd`|Print current/working directory|
|`mkdir`|Make direktories|
|**File operation**||
|`mv`|Move (rename] files|
|`touch`|Change file timestamp (create empty file)|
|**Utils**||
|`time ls -la`|Measure how long a program takes to run (measure the command ls -la)|
|`wget https://github.com -O github_com.html`|Download from network (download from github.com)|
|`ls -la \| grep 'Documents'`|Print lines that matches pattern (lines that contains 'Documents')|
|`sed 's/span/i' index.html`|Filtering and transforming text (replace span with i)|
|`tar -cvzf thomsmed_home_backup.tar.gz /home/thomsmed`|Archive (-c) and zip using gzip (-z) content at path (-f) '/home/thomsmed', and do it verbosely (-v)|
|`tar -xvzf thomsmed_home_backup.tar.gz`|Extract (-x) and decompress using gzip (-z) the content at path (-f) 'thomsmed_home_backup.tar.gz', and do it verbosely (-v)|

## Usefull stuff

### System information

```bash
# Infomation about CPU(s)
cat /proc/cpuinfo

# Word size
getconf WORD_BIT

# Long size
getconf LONG_BIT

```

### List all users on system

```bash
cat /etc/passwd
# Each line shows:
# - Username
# - Encrypted password (X means the password is stored in /etc/shadow
# - User ID number
# - User group ID number
# - Full name of the user
# - Home directory
# - Login shell
```

### User management

```bash
# friendlier front for other low level commands
adduser thomsmed
addgroup mygroup
```

### Debugging and inspection

```bash
strings ./MyProgram # View ASCII strings in binary
objdump -t ./MyProgram # View binary and symbols
time -p ./MyProgram # Time profiling
strace ./MyProgram # Log system calls and signals
```
