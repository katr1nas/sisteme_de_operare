# Lab 2: Meet the OS You Will Build

## Part 2. Using xv6

1. Three programs xv6 ships with: cat, echo, wc (also ls, grep, mkdir, rm, kill, sh).
2. A pipe needs processes (the shell starts the two commands as separate processes with fork and exec) and inter-process communication (the pipe system call gives a kernel buffer connecting the output of one process to the input of the other).
3. The xv6 shell is a small version of the Linux shell: it has the same basics (running programs, pipes, redirection) but far fewer features and commands.

## Part 3. Reading the source

1. user/cat.c uses read and write in its main loop: read(fd, buf, ...) asks the kernel to copy bytes from the open file into the buffer, and write(1, buf, n) asks the kernel to send those bytes to file descriptor 1 (the screen). It also uses open and close (ask the kernel to open and release a file) and exit (end the process).
2. sys_read is implemented in kernel/sysfile.c at line 69 (sys_write is at line 83).
3. The code in kernel/ is the operating system itself and runs privileged, while the code in user/ is ordinary programs that can only ask the kernel for services through system calls.

## Note on sleep.c

In this version of xv6 the sleep system call is named pause (see user/user.h and kernel/syscall.h: SYS_pause), so user/sleep.c calls pause().
