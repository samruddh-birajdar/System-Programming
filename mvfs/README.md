# MVFS — Mini Virtual File System

> A lightweight, inode-based virtual file system implemented in **C**, simulating core operating system file system concepts with a custom command-line shell.

---

## Overview

**MVFS** is an educational project that simulates how a real file system works — entirely in memory. It implements:

- **Inodes** to store file metadata
- **File descriptors** to track open files
- **Permissions** (read, write, read+write)
- **A shell** to interact with the file system using commands

Everything runs in **RAM** — no real disk writes. Perfect for understanding OS internals without touching the actual disk.

---

## Features

| Feature | Description |
|---|---|
| File Creation | Create files with custom permissions |
| Read / Write | Read and write data with offset tracking |
| Deletion | Free inodes and file table entries |
| File Listing | List files with `ls` and `ls -a` |
| Statistics | View detailed info with `stat` |
| Manual Pages | Get help for any command with `man` |
| Permission System | Read (1), Write (2), Read+Write (3) |
| Error Handling | 8 distinct error codes |

---

## Architecture

BootBlock → SuperBlock → Inode List (DILB) → FileTable → UAREA (UFDT)

| Component | Purpose |
|---|---|
| BootBlock | Holds boot information |
| SuperBlock | Tracks total and free inodes |
| Inode | Stores file metadata and data buffer |
| FileTable | Represents an open file |
| UAREA | Process context with UFDT |

---

## Data Structures

### BootBlock
```c
struct BootBlock {
    char Information[100];
};

struct SuperBlock {
    int TotalInodes;
    int FreeInodes;
};

struct Inode {
    char FileName[20];
    int InodeNumber;
    int FileSize;
    int ActualFileSize;
    int FileType;
    int ReferenceCount;
    int Permission;
    char *Buffer;
    struct Inode *next;
};

struct FileTable {
    int ReadOffset;
    int WriteOffset;
    int Mode;
    PINODE ptrinode;
};

struct UAREA {
    char ProcessName[20];
    PFILETABLE UFDT[MAXOPENFILES];
};

```

**Build and Run**
gcc MVFS.c -o mvfs
./mvfs


gcc -Wall -Wextra MVFS.c -o mvfs
./mvfs


**USAGE**
MVFS : > creat Ganesh.txt 3
File succesfully created with FD : 3

MVFS : > ls
Ganesh.txt

MVFS : > write 3
Enter the data that you want to write into the file
Hello MVFS
10 bytes gets succesfully written into the file

MVFS : > stat Ganesh.txt
File name : Ganesh.txt
Inode number : 1
File size : 50
Actual File size : 10
Reference Count : 1
File Permission : Read + Write
File type : Regular File

MVFS : > read 3 10
Read operation is succesful
Data from file is :
Hello MVFS

MVFS : > unlink Ganesh.txt

MVFS : > ls
(no files)

MVFS : > exit
Thank you for using MVFS
Deallocating all resources of MVFS
