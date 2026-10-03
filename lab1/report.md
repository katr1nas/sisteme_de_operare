# Lab 1: The OS as a Resource Manager

## Part 1. Files and directories

Observe:
1. The files note.txt and renamed.txt are owned by user ubuntu, group ubuntu.
2. After chmod 600, ls -l shows -rw-------. The first character - means a regular file. The next three (rw-) are the owner's permissions: read and write. The next three (---) are the group's and the last three (---) are everyone else's: no access. So only the owner can touch the file.
3. /etc holds system-wide configuration files. /home holds the home directories of regular users.

Interesting output (same file before and after chmod 600):

    -rw-rw-r-- 1 ubuntu ubuntu 24 Oct  1 23:13 note.txt
    -rw------- 1 ubuntu ubuntu 24 Oct  1 23:13 note.txt

The OS removed access for group and others, while the data stayed the same.

## Part 2. Processes

Observe:
1. PID 1 is systemd. It is the first process started by the kernel and the ancestor of all other user-space processes; it starts and supervises the rest of the system's services.
2. `ps aux | wc -l` printed 102. One line is the header, so about 101 processes were running on the idle VM.
3. The State line in /proc/<pid>/status for my sleeping process says `S (sleeping)`: the process is waiting (for the timer) and is not using the CPU.

Interesting output (what the kernel exposes about a process):

    Name:   sleep
    State:  S (sleeping)
    Pid:    1924
    PPid:   1571

PPid 1571 is my bash shell, which started sleep. This shows the parent-child process tree maintained by the OS.

## Part 3. Memory

Observe:
1. The VM has 3.8 GiB of total RAM (MemTotal 3995108 kB) and 3.1 GiB free; 3.5 GiB is available when reclaimable cache is counted.
2. Swap is an area on disk that the OS uses as an extension of RAM: when memory runs short, it moves rarely used pages there. On this VM swap is 0B, so none is configured.
3. A bare sleep process has VmRSS of 1760 kB, about 1.7 MB of real RAM. It is small, but not zero: even a program that does nothing needs memory for its code, the C library and its stack. Its virtual size (VmSize 5684 kB, seen in Part 2) is bigger than the resident part, because the OS only keeps in RAM the pages the process actually uses.

Interesting output:

    Mem:   total 3.8Gi  used 275Mi  free 3.1Gi  buff/cache 618Mi  available 3.5Gi
    Swap:  0B

Most of the memory not in "free" is buff/cache: the OS uses spare RAM to cache files and gives it back to programs when needed.

## Part 4. Devices and storage

Observe:
1. The root filesystem / is mounted on /dev/sda1 (partition 1 of the disk sda, type ext4, 19G, 11% used).
2. /dev/console stands for the system console (the terminal the OS talks to). Another example is /dev/sda, which stands for the whole 20G virtual disk.
3. "Everything is a file" means the OS gives disks, terminals and other devices the same file-like interface as normal files: they appear as entries in /dev and are used with the same file operations (open, read, write).

Interesting output:

    NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    sda       8:0    0   20G  0 disk
    ├─sda1    8:1    0   19G  0 part /
    ├─sda15   8:15   0   99M  0 part /boot/efi
    └─sda16 259:0    0  923M  0 part /boot

One physical disk is split by the OS into partitions, each mounted at its own place in the single directory tree.

## Conclusion

The OS manages four resources: files, processes, memory and devices. I saw them with: ls -l (files), ps aux (processes), free -h (memory), lsblk (devices and storage).
