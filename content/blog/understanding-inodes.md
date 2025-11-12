+++
title = "Understanding inodes in Linux"
author = "Suraj Narwade"
date = "2025-11-12T20:00:46.064Z"
category = "Blog"
series = ["Linux for Platform Engineers"]
+++


# What is inode?

An inode (short for index node) is a data structure in a Linux filesystem that stores metadata about a file.
Each file or directory in Linux has an inode associated with it, which contains,

- Inode Number
- Uid
- Gid
- Size
- Blocksize
- Mode
-  Number of links
-  ACLs, etc

but it does not contain the filename, interesting right, more on that later.

## Viewing Inodes

### view inode number of file or directory

you can use following command to view inode number of any file or directory,

```
ls -i filename
```

### Inspect inode details of a file

```
stat filename
```

## Why Inode usage matters?

let's first use following command and check inode usage,

```
df -i 
```

as we can see, inodes are finite. even if you have free disk space but if you hit the inode limit, you won't be able to create files.

you can see this issue in Kubernetes/Container world as shown below,

```
Error creating container: no space left on device
```


### Why inodes don’t contains filename?

Inodes in Linux do not contain filenames — only metadata like permissions, size, and data block locations.
Filenames are stored separately in directory entries, which map names to inode numbers.

This design offers three key advantages:

* Supports hard links – multiple filenames can point to the same inode.

let's look at quick example,

here we are hard link between 2 files and can see same inode number.

```
$ ls -li
total 0
1573252 -rw-rw-r-- 2 vagrant vagrant 0 Nov 12 19:59 hello1.txt
1573252 -rw-rw-r-- 2 vagrant vagrant 0 Nov 12 19:59 hello2.txt
```

* Fast renames/moves – only the directory entry changes, not the file’s inode.

* Cleaner separation – metadata (inode) and naming (directory) are managed independently.

In short, the inode describes the file’s identity, while the directory describes how you find it.

## Who decides how many inodes one can have?

When you run a command like:

```
mkfs.ext4 /dev/sda1
```

you’re not just creating space for files —
you’re also creating the inode table: a fixed number of inodes that will exist on that filesystem.

By default, ext4 and similar filesystems create one inode per X bytes of disk space (usually 1 inode per 16 KB of data space).

You can control this with:

```
mkfs.ext4 -i 16384 /dev/sda1
```

This means:

"Create 1 inode for every 16,384 bytes of storage."

Or you can set an absolute count:

```
mkfs.ext4 -N 500000 /dev/sda1
```

This means:

"Create exactly 500,000 inodes."

one last question came to my mind was,

### Where Inodes Are Stored?

Inodes are stored on disk within the filesystem itself, not in memory or a separate location.

When a filesystem (like ext4) is created, it divides the disk into block groups.
Each block group contains:

* A portion of the actual data blocks (where file contents live)
* A block bitmap (tracks which data blocks are in use)
* An inode bitmap (tracks which inodes are in use)
* An inode table (stores the actual inode structures)

So, inodes live inside the inode table — a reserved area of disk blocks specifically allocated for inode storage.


Hope this blog makes clear understanding of what inode is, why it matters :) 

