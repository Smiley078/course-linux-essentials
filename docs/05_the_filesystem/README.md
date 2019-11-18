---
description: As a linux power user you need to know your way around the filesystem
title: 05 - The Filesystem
---

# Chapter 05 - The Filesystem

A filesystem typically allows us to store both files and directories. **Files are used to store data** such as text, graphics and programs. **Directories** (aka "folders") are used to provide a **hierarchical organization structure**. Next to our data, the filesystem also stores **metadata** such as who's the owner of the file, creation and modification timestamps, size information, ...

## On Windows

The way the linux filesystem is structured is quitte different from the way Windows handles it. On a Windows system, the top level of the directory structure is called `My Computer`.

![My Computer on Windows](./img/windows_my_computer.png)

Each physical device (hard drive, DVD drive, USB thumb drive, network drive, etc.) shows up under `My Computer`, each assigned a drive letter, such as `C:` or `D:`.

![Device Hierarchy on Windows](./img/windows_drives.png)

Each device may have a certain filesystem on it (examples are FAT, FAT32, NTFS, ...).

## On Linux

Like Windows, a Linux directory structure has a top level, however it is not called `My Computer`, but rather the **root directory** and it is symbolized by the `/` character.

There are also **no drives in Linux**. Each physical device is accessible under a directory, not a drive letter.

![Linux Root](./img/linux_root.png)

Linux inherits many of its concepts of filesystem organization from its Unix predecessors. As far back as 1979, Unix was establishing standards to control how compliant systems would organize their files.

The **Linux File system Hierarchy Standard** (checkout [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/)), or FHS for short, is a prescriptive standard maintained by the Linux Foundation that establishes the organizational layout that Linux distributions should uphold for **interoperability**, **ease of administration**, and the **ability to implement cross-distro applications reliably**.

::: tip Everything is a File
One important thing to mention when dealing with these systems is that Linux implements just about **everything as a file**. This means that a text file is a file, a directory is a file (simply a list of other files), a printer is represented by a file (the device drivers can send anything written to the printer file to the physical printer), a serial port is a file, etc.

Although this is in some cases an oversimplification, it informs us of the approach that the designers of the system encouraged: passing text and bytes back and forth and being able to apply similar strategies for editing and accessing diverse components.
:::

## Finding your Way

Before actually delving into the file system layout, you need to know a few basics about how to navigate a file system from the command line. This course will cover the bare minimum here to get you on your feet.

The first thing you need to do is orient yourself in the filesystem. There are a few ways to do this, but one of the most basic ways is with the `pwd` command, which stands for "print working directory":

```bash
nico@biosdeb:~$ pwd
/home/nico
```

This simply returns the full path of the directory you are currently located in, for example `/home/nico`.

To see what files are in the current directory, you can issue the `ls` command, which stands for "list".

```bash
nico@biosdeb:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
```

This will give an overview of all directories and files in your current directory.

The `ls` command can be used to display the contents of a directory as well as detailed information about the files that are within a directory. The `ls` command can take some option flags. Flags modify the commands default behavior to either process or display the data in a different way.

The first most common option is probably `-l`, which forces the command to output information in long-form:

```bash
nico@biosdeb:~$ ls -l
total 32
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Desktop
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Documents
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Downloads
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Music
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Pictures
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Public
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Templates
drwxr-xr-x 2 nico nico 4096 Nov  3 13:54 Videos
```

This produces output with one line for each file or directory (the name is on the far right). This has a lot of information that we are not interested in right now. One part we are interested in though is the very **first character**, which tells us what **kind of file** it is. The three most common types are:

* `-`: Regular file
* `d`: Directory (a file of a specific format that lists other files)
* `l`: A hard or soft link (basically a shortcut to another file on the system)

The second most important option to the `ls` command is the `-a` flag, which lists all files, including hidden files. In Linux, files are **hidden** automatically if they begin with a dot `.`:

```bash
$ ls -a
.   bin   dev  home  log.txt     media  opt   root  sbin     srv  tmp  var
..  boot  etc  lib   lost+found  mnt    proc  run   selinux  sys  usr
```

::: tip .git directory
Hidden files always start with a `.`. That is why the `.git` directory in a git repo is also prefixed with a dot `.`.
:::

The first two entries, `.` and `..` have a special meaning. The `.` directory is a shortcut that means "the current directory". This is often used when executing shell scripts in the current directory. The `..` directory is a shortcut that means "the current directory's parent directory". In other words, one directory up.

## Traversing the Filesystem

Now that you can find out where you are in the file system and see what is around you, it is time to learn how to move throughout the file system.

To change to a different directory, you issue the `cd` command, which stands for "change directory":

```bash
cd /home
```

The first `/` character represents the **root directory** while each other `/` character is used to separate the directory names.

You can follow the `cd` command with either an absolute or a relative pathname.

* An **absolute path** is a file path that specifies the location of a directory from the top of the directory tree (the root of the filesystem `/`). Absolute paths begin with a "/", as can be seen in the example above.
* A **relative path** is a file path that is relative to the current working directory. This means that instead of defining a location from the top of the directory structure, it defines the location in relation to where you currently are.

For instance, if you want to move to the home directory of the user `nico`, while in the directory `/home`, you can issue the command:

```bash
nico@biosdeb:/home$ cd nico
nico@biosdeb:~$
```

The lack of the `/` from the beginning tells the shell to use the current directory as the base for looking for the path.

::: tip
Under this `/home` directory there will be a directory for most users on the system. The directory name will be the same as the name of the user.
:::

This is where the `..` directory link comes in handy. To move to the parent directory of your current directory, you can type:

```bash
nico@biosdeb:~$ cd ..
nico@biosdeb:/home$
```

This will jump 1 level up towards the root of the filesystem.

Your home directory is a very important directory. To begin with, when you open a shell, you should automatically be placed in your home directory.

```bash
nico@biosdeb:~$
```

Additionally, your home directory is one of the few directories where you have the full control to create and delete additional files and directories. On most Linux distributions, the only users who can access any files in your home directory are you and the administrator on the system (the `root` user).

There is a shortcut to traverse to your home directory, and that is by issueing the `cd` command without any arguments.

```bash
nico@biosdeb:/$ cd
nico@biosdeb:~$
```

This brings you immediately to your home directory.

Another way to achieve this is to use the `~` symbol, which symbolizes the path to your home directory.

```bash
nico@biosdeb:/$ cd ~
nico@biosdeb:~$
```

## An Overview of the Linux Filesystem Layout

The first thing you need to know when viewing a Linux file system is that the file system is contained within a single tree, regardless of how many devices are incorporated.

What this means is that all components accessible to the operating system are represented somewhere in the main file system. If you use Windows as your primary operating system, this is different from what you are used to. In Windows, each hard drive or storage space is represented as its own file system, which are labeled with letter designations (C: being the standard top-level directory of the system file hierarchy and additional drives or storage spaces being given other letter labels).

In Linux, every file and device on the system resides under the **root**, which is denoted by a starting `/`. This is not to be confused with the home directory of the *root* user which is located at `/root`.

Thus, if one wanted to go to the top-level directory of the entire operating system and see what is there, one could use the `cd` command followed by the root directory `/` to traverse to the root directory and then simply use the `ls` command to display the files and directories there.

```bash
nico@biosdeb:~$ cd /
nico@biosdeb:/$ ls
bin   home            lib64       mnt   run   tmp      vmlinuz.old
boot  initrd.img      libx32      opt   sbin  usr
dev   initrd.img.old  lost+found  proc  srv   var
etc   lib             media       root  sys   vmlinuz

```

Every file, device, directory, or application is located under this one directory. Under this, we can see the beginnings of the rest of the directory structure.

One of the principles guiding the organization of the file system is to allow it to be split across multiple disk partitions (or multiple disks) in a rational manner, and to allow appropriate pieces of it to be shared between machines. Key to this is the notion of the root partition (/, the parent of the entire file system).

When Linux boots, the kernel attaches a single file system partition all by itself. This is known as the **root partition**. Any other partitions that need to be attached are mounted by the `mount` command, usually under control of entries in the file `/etc/fstab`. Because in the early stages of startup, only the root file system is available, it **must contain everything needed for the system to function** and attach the other pieces of the file system.

Tools on the root partition include the `init` program (which starts all the other processes), a shell, `mount` and the `/etc/fstab` file. The File System Hierarchy standard specifies a number of directories that must lie within the root partition.

Below is a typical Linux file system hierarchy.

![A typical Linux file system hierarchy](./img/typical_filesystem_hierarchy.png)

Let's take a closer look at the different directories which can be found under the root.

**/bin** - This directory contains basic commands and programs that are needed to achieve a minimal working environment upon booting. These are kept separate from some of the other programs on the system to allow you to boot the system for maintenance, even if other parts of the file system may be damaged or unavailable. If you search this directory, you will find that both `ls` and `pwd` reside here. The `cd` command is actually built into the shell we are using (bash), which is in this directory too.

**/boot** - This directory contains the actual files, images, and kernels necessary to boot the system. While `/bin` contains basic, essential utilities, `/boot` contains the core components that actually allow the system to boot. If you need to modify the **bootloader** on your system, or if you would like to see the actual **kernel files** and **initial ramdisk** (initrd), you can find them here. This directory must be accessible to the system very early on.

**/dev** - This directory houses the **files that represent devices** on your system. Every hard drive, terminal device, input or output device available to the system is represented by a file here. Depending on the device, you can operate on the devices in different ways. For instance, for a device that represents a hard drive, like `/dev/sda`, you can mount it to the file system to access it. On the other hand, if you have a file that represents a line printer like `/dev/lpr`, you can write directly to it to send the information to the printer.

**/etc** - Stands for "Editable Text Configuration". This is one area of the file system where you will spend a lot of time if you are working as a system administrator. This directory is basically a **configuration directory for various system-wide services**. By default, this directory contains many files and subdirectories. It contains the configuration files for most of the activities on the system, regardless of their function. In cases where multiple configuration files are needed, many times an application-specific subdirectory is created to hold these files. If you are attempting to configure a service or program for the entire system, this is a great place to look.

**/home** - This location contains the **home directories of all of the users on the system** (except for the administrative user, root). If you have created other users, a directory matching their username will typically be created under this directory. Inside each home directory, the associated user has write access. Typically, regular users only have write access to their own home directory. This helps keep the file system clean and ensures that not just anyone can change important configuration files. Within the home directory, that are often hidden files and directories (represented by a starting dot) that allow for user-specific configuration of tools. You can often set system defaults in the `/etc` directory, and then each user can override them as necessary in their own home directory.

**/lib** - This directory is used for all of the **shared system libraries** that are required by the `/bin` and `/sbin` directories. These files basically provide functionality to the other programs on the system. This is one of the directories that you will not have to access often.

**/lost+found** - This is a special directory that **contains files recovered** by `/fsck`, the Linux file system repair program. If the file system is damaged and recovery is undertaken, sometimes files are found but the reference to their location is lost. In this case, the system will place them in this directory. In most cases, this directory will remain empty. If you experience corruption or any similar problems and are forced to perform recovery operations, it's always a good idea to check this location when you are finished.

**/media** - This directory is typically empty at boot. Its real purpose is simply to provide a location to **mount removable media** (like a USB drive). If your Linux operating system ever mounts a media disk and you are unsure of where it placed it, this is a safe bet.

**/mnt** - This directory is similar to the `/media` directory in that it exists only to serve as an **organization mount point for devices**. In this case, this location is usually used to **mount file systems like external hard drives**, etc. This directory is often used in a VPS environment for mounting network accessible drives. If you have a file system on a remote system that you would like to mount on your server, this is a good place to do that.

**/opt** - This directory's **usage is rather ambiguous**. It is used by some distributions, but ignored by others. Typically, it is used to **store optional packages**. In the Linux distribution world, this usually means packages and applications that were not installed from the repositories. For instance, if your distribution typically provides the packages through a package manager, but you installed program X from source, then this directory would be a good location for that software. Another popular option for software of this nature is in the `/usr/local` directory.

**/proc** - The `/proc` directory is actually more than just a regular directory. It is actually a **pseudo-file system** of its own that is mounted to that directory. The proc file system does not contain real files, but is instead dynamically generated to **reflect the internal state of the Linux kernel**. This means that we can check and modify different information from the kernel itself in real time. For instance, you can get detailed information about the processor and its cores by displaying the content of `/proc/cpuinfo`.

**/root** - This is the **home directory of the administrative user** (called "root"). It functions exactly like the normal home directories, but is housed here instead.

**/run** - This directory is for the operating system to write **temporary runtime information** during the early stages of the **boot process**.

**/sbin** - This directory is much like the `/bin` directory in that it contains **programs deemed essential for using the operating system**. The distinction is usually that `/sbin` contains commands that are available to the **system administrator**, while the other directory contains programs for all of the users of the system. Examples of such binaries are `iptables`, `mkfs`, `lsmod`, `chroot`, ...

**/selinux** - This directory contains **information involving security enhanced Linux**. This is a kernel module that is used to provide access control to the operating system.

**/srv** - This directory is used to contain **data files for services provided by the system**. In most cases, this directory is not used too much because its functionality can be implemented elsewhere in the filesystem.

**/tmp** - This is a directory that is used to store **temporary files** on the system. It is **writable by anyone** on the computer and **does not persist upon reboot**. This means that any files that you need just for a little bit can be put here. They will be automatically deleted once the system shuts down.

**/usr**  - Stands for **Unix System Resources**. This directory is one of the largest directories on the system. It basically includes a set of folders that look similar to those in the root `/` directory, such as `/usr/bin` and `/usr/lib`. This location is basically used to store all **non-essential programs, their documentation, libraries, and other data** that is not required for the most minimal usage of the system. This is where most of the files on the system will be stored. Some important subdirectories are `/usr/local`, which is an alternative to the `/opt` directory for storing locally compiled programs. Another interesting thing to check out is the `/usr/share` directory, which contains documentation, configuration files, and other useful files.

**/var** - This directory is supposed to contain **variable data**. In practice, this means it is used to contain information or directories that you expect to grow as the system is used. For example, **system logs** and **backups** are housed here. Another popular use of this directory is to store **web content if you are operating a web server**.

## Basic Filesystem Manipulation

While looking at files and directories is all nice, but in the end you will probable want to create your own directories and files. For this you will need to enlarge your command line toolset a bit more.

### Viewing File Content

To output the content of one or more files to the terminal, use the `cat` command. The general syntax is `cat file1 file2 ...`. The `cat` command actually **concatenates the content of the files specified** and outputs it to the terminal.

To view for example the file systems that are mounted on your current setup, you can view the file `/etc/fstab`.

```bash
cat /etc/fstab
```

Or to display some information about the CPU of your system, check the kernel mapped file `/proc/cpuinfo`.

```bash
cat /proc/cpuinfo
```

Want to find out what shells are available on your system, then display the content of the file `/etc/shells` using the command `cat /etc/shells`.

```bash
nico@biosdeb:/etc$ cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
```

### Creating a File

There are several ways of creating a new file, including using a program designed to edit a file (a text editor).

One of the most common command line tools used to create an empty file, is the `touch` command.

```bash
nico@biosdeb:~$ touch hello
nico@biosdeb:~$ ls
Desktop  Documents  Downloads  hello  Music  Pictures  Public  Templates  Videos
```

::: tip Touch
Touch is actually not intended to be used as a command to create files. It's primary use is to update the timestamps (creation and modification) on files to the current time. As a side-effect, if the file is non-existent, it is created.
:::

### Editing files

A file can be edited using a text-editor. Many command line editors exist. From basic usage to full blown programming tools.

Some beginner friendly editors are `nano` and `joe`. More common editors used for programming are `vim` and `emacs`.

`nano` should be pre-installed on your system. Use it to edit the content of a file in your home directory. If you specify a file that doesn't exist yet, nano will create it for you.

```bash
nico@biosdeb:~$ nano hello
```

![Nano Text Editor](./img/nano.png)

Some shortcuts in nano:

* `CTRL-x`: exit
* `CTRL-c`: cancel
* `CTRL-o`: write to filesystem
* `CTRL-k`: cut
* `CTRL-u`: uncut

## Quick Recap on Filesystem Commands

The most used commands to traverse and manipulate the file system of a Linux system are listed in the table below. You can always use the man-command to get a more detailed description.

| Command | Options | Arguments | Description |
| ---- | ---- | ---- | ---- |
| `pwd` |  |  | Print working dir |
| `ls` | `-l` (long format), `-a` (all), `-h` (human readable) | absolute path / relative path / `~`, `..` or `.` | List files and directories |
| `cd` |  | absolute path / relative path / `~`, `..` or `.` | Change working dir |

<!-- | `cp` | ---- | ---- | Copy files |
| `rm` | ---- | ---- | Remove files |
| `mv` | ---- | ---- | Move files |
| `cat` | ---- | ---- | Concatenate files and output to terminal |
| `touch` | ---- | ---- | Create an empty file |
| `mkdir` | ---- | ---- | Make directory |
| `rmdir` | ---- | ---- | Remove an empty directory | -->


## Shell Globbing

Globbing is the process of expandeding wildcards (`*`, `?`, ...) to match filenames **before a command is actually executed**. In other words, the command shell uses globbing for filename completion.

These wildcard patterns do not only match file names. The names being matched by a pattern can be anything: files, directories, symbolic links, etc. The shell will try to expand any tokens on the command line that contain unquoted wildcard characters into **existing** pathnames in the file system. These patterns cannot generate any names that do not exist. The patterns must always match existing names.

### Examples of Globbing

The asterisk `*` matches any combination of characters (even none).

* `echo *` will give a list of all the files in the current directory.
* `echo *.log` will give you a list of the files that end in `.log` in the current directory
* `echo /dev/sd*` will give you a list of all the devices that are available starting with `sd`
* `echo *log*` will expand to a list of files that contain the word `log` (this would for example match `apache.log` and `logdns.txt`)

::: warning When no matches are found
If the shell cannot match the glob to existing files, it will not perform any expansion and run the command with the literal symbols. If you for example enter `echo *sdfduhreiutret`, it will literally print `*sdfduhreiutret`.
:::

To match a single character, one can use the question mark `?`.

* `echo h?llo` would match both `hello` and `hallo` for example

If you do not wish a glob character to be expanded by the shell, you can always enclose it into single quotes:

```bash
echo '*'
```

which will just output `*`.

It is also important to know that the shell will only perform expansion a once, before the command is executes. This also means that if a wildcard symbol makes it to the executing command, it's up to command to deal with it as needed.

::: warning Hidden files
Shell globs do not match hidden files (files that start with a dot `.`). Matching hidden files can be accomplished by explicitly specifying a pattern with the dot as for example `.*`. This will however also match the current `.` and parrent directory `..`. To exclude these but match other dotfiles, use a pattern as `.[^.]*` (dot followed by anything but a dot).
:::

## Finding Files

An action often required on any operating system is the ability to search for files inside the filesystem. Linux has an extensive tool just for this, called `find`, which can even be used to execute commands against each file or directory found.

To search for a file use the following syntax:

```bash
find <where> -name <filename>
```

The command excepts wildcards such as `*` and `?` inside the filename but they should be enclosed in single quotes `''` to protect them from shell globbing expansion.

Find can also be used with regular expressions to make more specific queries.

Some examples:

* `find ~ -name '*.zip'` to find all the zipfiles in your home folder
* `find ~ -iname readme.md` to find all the markdown readme files (case insensitive) inside your home dir.
* `find ~ -size +1G` to find all files that are greater that 1 GB in size

::: warning Getting Permission Denied
Want to get rid of the permission denied entries, then redirect the error output to `/dev/null` as shown in the example `find ~ -name '*.zip' 2>/dev/null`. More on this later.
:::

## Grepping Files

Grep is a command line tool that can be used to filter lines from a file or input stream. Grep can even by used to act upon multiple files. This means that using shell globbing, you can filter lines from multiple files. In this case, grep prints the name of the file prefixed before the match output.

Probable the two most used flags are `-i` for case-insensitive matching and `-v` for inverting the search, in other words, print the lines that don't match.

Some examples

* `grep help /usr/share/dict/words` matches all the words that contain the word `help`
* `grep -c -v help /usr/share/dict/words` count how many words that do not contain the word `help`

Grep also understands regular expressions, a more advanced way of building queries to search in text. This is however a chapter on itself.

::: info Grep on an input stream
When you do not pass a filename to grep it acts on the input stream. This means that you can pipe output from other command or you can even just type stuff in. Try out the following command `grep bye`. Type CTRL-D to send an end-of-file and make grep stop.
:::

## Other Useful Commands

| Command | Description |
| ---- | ---- |
| `diff` | Compare files line by line |
| `file` | Determine the type of a file |
| `less` | Paging through big files |
| `more` | Paging through big files (older variant) |
| `locate` | Find files via indexing |
| `head` | Show top of output |
| `tail` | Show bottom of output |
| `sort` | Order lines |
| `passwd` | Change a password of a user |
| `chsh` | Change shell |
| `info` | GNU variant of man pages |