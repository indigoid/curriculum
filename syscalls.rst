Syscalls
********

What are syscalls?

Tracing syscalls
================
All modern UNIX and UNIX-like operating systems supply some sort of tool
for tracing system calls.

* for Solaris, ``truss``
* for the BSD family, ``ktrace``
* for Linux, ``strace``

These commands all perform approximately the same task but the manner of
use differs. Refer to the manual page for each for details.
The basic functions of a system call tracer are:

* spawn a new process, or attach to a specified (by process ID) process
* intercept all system calls (this slows the traced process down a bit),
  capturing the arguments passed to each call
* capture signals received by that process
* optionally, do all of the above for any child processes

System call trace utilities will not follow child processes after they
call ``setuid()`` to change their effective user ID.

Using ``strace``
================
``strace`` is a system call tracer for Linux systems.
process and displays information about some or all system calls made by
that process.
Optionally ``strace`` can also spawn the process for you. A quick
example: ::

  $ strace -e trace=file cat /etc/ethers
  execve("/bin/cat", ["cat", "/etc/ethers"], [/* 22 vars */]) = 0
  access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
  open("/etc/ld.so.cache", O_RDONLY)      = 3
  open("/lib64/libc.so.6", O_RDONLY)      = 3
  open("/etc/ethers", O_RDONLY)           = 3
  # see man ethers for syntax

In this example ``strace`` has been instructed to only show file-related
system calls. The command being traced is ``cat /etc/ethers``, which
``strace`` will spawn.

``strace`` can be particularly useful for tracking down errors where you
suspect that a process is not looking for a file in the correct
location, and audit logs do not provide enough detail.
In the above example, the output shows that as the ``cat`` command was
starting, it checked (via the ``acccess`` system call) for the existence
of the file ``/etc/ld.so.preload``, and the result was ENOENT, indicating
that that file did not exist.

Pre-existing processes can be traced via the ``-p`` commandline
parameter; in this example, the shell expands ``$$`` to the process ID
of the shell invoking ``strace``, and in another terminal window a
SIGTERM signal was then sent to that process ID to generate some
interesting trace output: ::

  $ strace -p $$
  Process 2149 attached - interrupt to quit
  wait4(-1, 0x7fff35f41bcc, WSTOPPED|WCONTINUED, NULL) = ? ERESTARTSYS (To be restarted)
  --- SIGTERM (Terminated) @ 0 (0) ---

``strace`` can do much more than has been shown here.
Refer to the manual page for the gritty details.

Common system calls
===================

``fork()`` and ``exec()``
-------------------------

``open()`` and ``close()``
--------------------------

``create()``, ``unlink()`` and ``stat()``
-----------------------------------------

``bind()`` and ``accept()``
---------------------------

``ioctl()``
-----------
