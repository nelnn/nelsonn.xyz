---
title: "rsync"
date: 2024-10-09T21:48:36+01:00
tags: ["code"]
---

If you don't know about rsync, you should get to know it now. Rsync is a Linux utility that allows you to backup files and directories to either a remote or local server. You can even stop syncing and be able to resume without starting over because rsync can pick up where if left off. 

Basic usage copied from the `man` page:

```bash
rsync -t *.c foo:src/
```
This would transfer all files matching the pattern *.c from the current directory to the directory src on the machine foo. If any of the files already exist on the remote system then the rsync remote-update protocol is used to update the file by sending only the differences. See the tech report for details.

```bash
rsync -avz foo:src/bar /data/tmp
```
This would recursively transfer all files from the directory src/bar on the machine foo into the
/data/tmp/bar directory on the local machine. The files are transferred in "archive" mode, which ensures
that symbolic links, devices, attributes, permissions, ownerships, etc. are preserved in the transfer.
Additionally, compression will be used to reduce the size of data portions of the transfer.
```bash
rsync -avz foo:src/bar/ /data/tmp
```
A trailing slash on the source changes this behavior to avoid creating an additional directory level at the destination.  You can think of a trailing / on a source as meaning "copy the contents of this directory" as opposed to "copy the directory by name", but in both cases the attributes of the containing directory are transferred to the containing directory on the destination. 

You can do this with a local machine of the same network. For example, to transfer files between two Macbooks, first enable *Remote Login* (assuming macOS Sonoma) in Settings -> General -> Sharing -> Advanced -> toggle the Remote Login option on the received end. At the bottom of the page you can see the local host name, we need that to send the files. Now, on the other computer we can just run
```bash
rsync [option] /src/foo/ username@localhostname:/data/foo
```

You can check out the full documentation by typing `man rsync` in the terminal.
