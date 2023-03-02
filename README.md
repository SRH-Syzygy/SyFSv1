# SyFSv1


> > Please note that rolling your own filesystem IS NOT RECOMMENDED. You're likely to make it a FAT style filesystem, and please, we don't need another one of those. However, for those of you who wish to defy convention, then we need to stop you making another FAT for sanity's sake. So here is a tutorial on how to avoid a FAT (of course, the design is up to you, so we can't give you a complete tutorial on how to make it). 
> > 
> > -- from OSDev.org, ["Roll Your Own Filesystem"](https://wiki.osdev.org/Roll_Your_Own_Filesystem)
> 
> ![how bout i do anyway?](https://i.kym-cdn.com/photos/images/original/001/094/230/bc1.png)

SyFSv1 ("Syzygy File System version 1") is (currently planned to be) a set of simple, easy-to-implement file systems for the current SRH Syzygy OS. This document describes the first minor version of SyFSv1. This version of SyFSv1 is akin to [BMFS](https://github.com/ReturnInfinity/BMFS) but modified for use on floppies and tiny-size hard disks.

## Features (or lack thereof)

+ Doesn't support subdirectory.
+ All files are consecutive.
+ Bounded root directory size (8 sectors; 128 entries for 512Byte sector.)

## Disk layout

+ FS Block (described below) (1 sector)
+ Root directory area (8 sector) 
+ Data area

## FS Block

|offset|size(bytes)|description|
|--|-|----|
|0x0|3|A short jump instruction to skip the FS Block.|
|0x3|1|SyFS major version number. 0x1 in this case.|
|0x4|1|SyFS minor version number. 0x0 in this case.|
|0x5|2|Number of bytes per sector, little-endian.|
|0x7|1|Number of reserved sectors, including the boot sector.|
|0x8|502|Bootloader code.|
|0x1fe|2|Boot sector signature 0x55aa.


## Root directory entry

Each entry 32 bytes.

|offset|size(bytes)|description|
|-|-|-|
|0x0|16|File name in ASCII. If shorter than 16 then it's padded with 0.|
|0x10|1|Attribute word|
|0x11|2|The LBA of the first sector of the starting block.|
|0x13|2|Creation time|
|0x15|2|Creation date|
|0x17|2|Last update time|
|0x19|2|Last update date|
|0x1b|4|File size in bytes*|
|0x1f|1|Reserved.|

### Time and date

Both time and date are stored in little-endian.

```
YYYYYYYM MMMDDDDD
hhhhhmmm mmmsssss
```

+ `Y`: Year. 7-bit. ranges from 0 to 127. 0 means 1970, which means it can represent up to year 2097.
+ `M`: Month. 4-bit. starts from 0 (i.e. 0 means January, 1 means February, etc.)
+ `D`: Day. 5-bit. starts from 0.
+ `h`: hour. 5-bit.
+ `m`: minute. 6-bit.
+ `s`: second. 5-bit. ranges from 0 to 30. represents even number seconds (i.e. multiply by 2).

