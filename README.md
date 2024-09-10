# Bandit

Bandit 是一个免费的 CTF 靶场

[OverTheWire: Wargames](https://overthewire.org/wargames/)

极好的工具：

- [explainshell.com - match command-line arguments to their help text](https://explainshell.com/)
- `man` 命令



## L0 helloworld

登录即可

```
ssh bandit0@bandit.labs.overthewire.org -p 2220
```

## L0->L1 readme

```
cat readme
```

可以拿到密码，下一关

```
ssh bandit1@bandit.labs.overthewire.org -p 2220
```



```
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```



## L1->L2 cat -

文件名是一个 `-` ，`cat -` 是没用的

```
cat ./-
```

```
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```



## L2->L3 space file

```
cat spaces\ in\ this\ filename
```

```
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```



## L3 -> L4 hidding file

```
cat inhere/...Hiding-From-You
2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```



## L4 -> L5 where ascii

从一堆文件中，找到人类可读的文本

```
find . -type f | xargs cat
或
bandit4@bandit:~/inhere$ find . -type f | xargs file
./-file00: data
./-file03: data
./-file08: data
./-file02: data
./-file04: data
./-file01: data
./-file07: ASCII text
./-file06: data
./-file05: data
./-file09: data
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```



## L5 -> L6 du

提示：大小是 1033

```
-0, --null
              Input  items  are  terminated  by a null character instead of by whitespace, and the quotes and backslash are not special
              (every character is taken literally).  Disables the end of file string, which is treated like any other argument.  Useful
              when input items might contain white space, quote marks, or backslashes.  The GNU  find  -print0  option  produces  input
              suitable for this mode.
```

> 常常使用 man，会变得高手

错误示范

```
find . -type f | xargs -d \n file
```

正确：

```
find . -type f -print0 | xargs -0 file
```

[explainshell.com - find . -type f -print0 | xargs -0 file](https://explainshell.com/explain?cmd=find+.+-type+f+-print0+|+xargs+-0+file)

这样就可以把所有文件类型打印出来



- grep -v 反转筛选
- tr '\n' '\0' 替换字符
- cut -d: -f1 以 `:` 切分字符串，取第一列，不加 -d 默认为 TAB



```
$ find . -type f -print0 | xargs -0 du -b | grep 1033
1033    ./maybehere07/.file2
```

-b 是显示 bytes，默认是 kb

```
> find . -type f -print0 | xargs -0 du -b | grep 1033 | cut -f2 | xargs cat
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```



## L6 -> L7 find

> The password for the next level is stored **somewhere on the server** and has all of the following properties:
>
> - owned by user bandit7
> - owned by group bandit6
> - 33 bytes in size

[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html) , [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html) , [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html) , [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) , [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html) , [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html) , [grep](https://manpages.ubuntu.com/manpages/noble/man1/grep.1.html)



```
> find / -type f | xargs du -b | grep 33 | cut -f2 | xargs ls -l | grep "bandit7"
-r--------  1 bandit7     bandit7                33 Jul 17 15:56 /etc/bandit_pass/bandit7
-rw-r-----  1 bandit7     bandit6                33 Jul 17 15:57 /var/lib/dpkg/info/bandit7.password
```

```
$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```



## L7 -> L8

> The password for the next level is stored in the file **data.txt** next to the word **millionth**

用 vim 按 `/` 搜索即可

```
dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```



## L8 -> L9 sort & uniq

> ## Level Goal
>
> The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once
>
> ## Commands you may need to solve this level
>
> grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

先整理，再计数，再过滤

```
> sort data.txt | uniq -c | grep " 1 "
   1 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```



## L9 -> L10 strings

> ## Level Goal
>
> The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several ‘=’ characters.
>
> ## Commands you may need to solve this level
>
> grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

```
$ strings data.txt | grep "==="
\a!;========== the
========== passwordf
========== isc
========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```



## L10 -> L11 base64

> ## Level Goal
>
> The password for the next level is stored in the file **data.txt**, which contains base64 encoded data
>
> ## Commands you may need to solve this level
>
> grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

```
$ cat data.txt | base64 -d
The password is dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```



## L11 -> L12 rot13

> ## Level Goal
>
> The password for the next level is stored in the file **data.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions
>
> ## Commands you may need to solve this level
>
> grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
>
> ## Helpful Reading Material
>
> - [Rot13 on Wikipedia](https://en.wikipedia.org/wiki/ROT13)

```
python3
```



```python
f = open("data.txt")
s = f.readline().encode()
f.close()
s = list(s)
for i in range(len(s)):
    c = s[i]
    if ord('a') <= c <= ord('z'):
        s[i] = ord('a') + ((c - ord('a') - 13) % 26)
    if ord('A') <= c <= ord('Z'):
        s[i] = ord('A') + ((c - ord('A') - 13) % 26)

print(bytes(s).decode())
# The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```



## L12 -> L13 unzip and unzip

> ## Level Goal
>
> The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)
>
> ## Commands you may need to solve this level
>
> grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd, mkdir, cp, mv, file

```
mktemp -d
cd /tmp/tmp.ibRfDOF4Lv
```

```python
f = open("data.txt")
ss = f.readlines()
f.close()
result = ""
for s in ss:
    result += s.split(": ")[1].split("  ")[0]
    result += ' '

print(result)
bb = b''.join([bytes.fromhex(r) for r in result.split(" ")])

with open("output", "wb") as fb:
    fb.write(bb)
```

或者

```
$ xxd -r data.txt | file -
/dev/stdin: gzip compressed data, was "data2.bin", last modified: Wed Jul 17 15:57:06 2024, max compression, from Unix
$ xxd -r data.txt | gzip -d | file -
/dev/stdin: bzip2 compressed data, block size = 900k
$ xxd -r data.txt | gzip -d | bzip2 -d | file -
/dev/stdin: gzip compressed data, was "data4.bin", last modified: Wed Jul 17 15:57:06 2024, max compression, from Unix
$ xxd -r data.txt | gzip -d | bzip2 -d | gzip -d | file -
/dev/stdin: POSIX tar archive (GNU)
$ xxd -r data.txt | gzip -d | bzip2 -d | gzip -d | tar -xO | file - # 这里的 -x 是解压，O是输出到输出流
/dev/stdin: POSIX tar archive (GNU)
$ xxd -r data.txt | gzip -d | bzip2 -d | gzip -d | tar -xO | tar -xO | bzip2 -d | tar -xO | gzip -d | file -
/dev/stdin: ASCII text
$ xxd -r data.txt | gzip -d | bzip2 -d | gzip -d | tar -xO | tar -xO | bzip2 -d | tar -xO | gzip -d
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```



## L13 -> L14 ssh with key

> ## Level Goal
>
> The password for the next level is stored in **/etc/bandit_pass/bandit14 and can only be read by user bandit14**. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. **Note:** **localhost** is a hostname that refers to the machine you are working on
>
> ## Commands you may need to solve this level
>
> ssh, telnet, nc, openssl, s_client, nmap



```shell
$ ssh bandit14@localhost -p 2220 -i sshkey.private
$ cat /etc/bandit_pass/bandit14
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
```

> 非预期：ps -aux 可以看到命令



## L14 -> L15 nc

> ## Level Goal
>
> The password for the next level can be retrieved by submitting the password of the current level to **port 30000 on localhost**.
>
> ## Commands you may need to solve this level
>
> ssh, telnet, nc, openssl, s_client, nmap
>
> ## Helpful Reading Material
>
> - [How the Internet works in 5 minutes (YouTube)](https://www.youtube.com/watch?v=7_LPdttKXPc) (Not completely accurate, but good enough for beginners)
> - [IP Addresses](https://computer.howstuffworks.com/web-server5.htm)
> - [IP Address on Wikipedia](https://en.wikipedia.org/wiki/IP_address)
> - [Localhost on Wikipedia](https://en.wikipedia.org/wiki/Localhost)
> - [Ports](https://computer.howstuffworks.com/web-server8.htm)
> - [Port (computer networking) on Wikipedia](https://en.wikipedia.org/wiki/Port_(computer_networking))



```shell
$ nc localhost 30000
MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS
Correct!
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
```



## L15 -> L16 ssl ncat

> ## Level Goal
>
> The password for the next level can be retrieved by submitting the password of the current level to **port 30001 on localhost** using SSL/TLS encryption.
>
> **Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.**
>
> ## Commands you may need to solve this level
>
> ssh, telnet, nc, ncat, socat, openssl, s_client, nmap, netstat, ss
>
> ## Helpful Reading Material
>
> - [Secure Socket Layer/Transport Layer Security on Wikipedia](https://en.wikipedia.org/wiki/Transport_Layer_Security)
> - [OpenSSL Cookbook - Testing with OpenSSL](https://www.feistyduck.com/library/openssl-cookbook/online/testing-with-openssl/index.html)



nc 不支持 ssl，ncat 可以

```
$ ncat --ssl localhost 30001
8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo
Correct!
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
```



## L16 -> L17 where is ssl

> ## Level Goal
>
> The credentials for the next level can be retrieved by submitting the password of the current level to **a port on localhost in the range 31000 to 32000**. First find out which of these ports have a server listening on them. Then find out which of those speak SSL/TLS and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.
>
> **Helpful note: Getting “DONE”, “RENEGOTIATING” or “KEYUPDATE”? Read the “CONNECTED COMMANDS” section in the manpage.**
>
> ## Commands you may need to solve this level
>
> ssh, telnet, nc, ncat, socat, openssl, s_client, nmap, netstat, ss



```
$ nmap localhost -p31000-32000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-03 14:34 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00073s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT      STATE SERVICE
31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.19 seconds
```



可以手动尝试：

```
$ ncat --ssl localhost 31790
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----
```



或者用一个 shell 脚本

```
for port in $(nmap -p31000-32000 --open localhost | grep 'open' | awk '{print $1}' | cut -d'/' -f1); do
	echo $port
    echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | ncat --ssl localhost $port
done
```



连进下一关

```
ssh bandit17@localhost -p 2220 -i L17.private_key
```



## L17 -> L18 diff

> ## Level Goal
>
> There are 2 files in the homedirectory: **passwords.old and passwords.new**. The password for the next level is in **passwords.new** and is the only line that has been changed between **passwords.old and passwords.new**
>
> **NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19**
>
> ## Commands you may need to solve this level
>
> cat, grep, ls, diff

```
$ diff passwords.old passwords.new
42c42
< bSrACvJvvBSxEM2SGsV5sn09vc3xgqyp
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

但是连上去之后，显示 `Byebye!` —— 根据提示，其实已经算下一关了



## L18 -> L19 no bash

> ## Level Goal
>
> The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.
>
> ## Commands you may need to solve this level
>
> ssh, ls, cat

简单，不用 bash 即可

```
ssh bandit18@bandit.labs.overthewire.org -p 2220 /bin/sh
cat readme
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```



## L19 -> L20 setuid and setgid

> ## Level Goal
>
> To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.
>
> ## Helpful Reading Material
>
> - [setuid on Wikipedia](https://en.wikipedia.org/wiki/Setuid)

```
$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

简单来说，就是如果设置了 setuid ，任何用户运行的时候，就会以文件的 owner 的权限执行，设置了 setgid ，就会以文件的 group 的权限执行。

用 `stat /path/to/file` 可以查看详细信息

```
$ stat bandit20-do
  File: bandit20-do
  Size: 14880           Blocks: 32         IO Block: 4096   regular file
Device: 259,1   Inode: 544320      Links: 1
Access: (4750/-rwsr-x---)  Uid: (11020/bandit20)   Gid: (11019/bandit19)
...
```

`4750` 是一个八进制数，每个八进制数转换成二进制，每个 bitmask 都对应一个权限。比如上面的 7 -> 0b111 就是 owner 的权限是 rwx；5 -> 0b101 就是同 group 的权限是 r-x，可读、可执行。

- 4 代表设置了 setuid —— 0b100 是 setuid，0b010 是 setgid
- 设置了 setuid 或 setgid 之后，owner 或 group 的 x 就会变成 s
- 如果只设置了 setuid 或 setgid，而没有设置可执行，就会是一个大 S —— 意味着这样的设置是异常的。



可以试一试

```
$ touch test
$ ls -l test
-rw-r--r-- 1 oldkingok oldkingok 0 Sep  4 13:10 test
$ ls -l test # 异常了
-rwSr--r-- 1 oldkingok oldkingok 0 Sep  4 13:10 test
$ ls -l test
-rwsr--r-- 1 oldkingok oldkingok 0 Sep  4 13:10 test
```





## L20 -> L21 send & recv

> ## Level Goal
>
> There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).
>
> **NOTE:** Try connecting to your own network daemon to see if it works as you think
>
> ## Commands you may need to solve this level
>
> ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)
>
> 登陆密码：0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO

```
$ nmap localhost
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-04 05:23 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00018s latency).
Not shown: 993 closed tcp ports (conn-refused)
PORT      STATE SERVICE
22/tcp    open  ssh
1111/tcp  open  lmsocialserver
1840/tcp  open  netopia-vo2
4321/tcp  open  rwhois
8000/tcp  open  http-alt
30000/tcp open  ndmps
50001/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.10 seconds
```

噢噢是我理解错了，是要用两个终端， `./suconnect` 去连接 `ncat`，然后从 `ncat` 发送和接受信息。

终端1：

```
$ ./suconnect 2222
```

终端2：

```
$ ncat -l localhost 2222
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```



## L21 -> L22 cron & crontab

> ## Level Goal
>
> A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.
>
> ## Commands you may need to solve this level
>
> cron, crontab, crontab(5) (use “man 5 crontab” to access this)

cron 和 crontab 是用来设置定时任务的，配置文件存在 /etc/cron.d/

```
bandit21@bandit:/etc/cron.d$ ll
total 44
drwxr-xr-x   2 root root  4096 Jul 17 15:59 ./
drwxr-xr-x 121 root root 12288 Aug  1 14:49 ../
-rw-r--r--   1 root root   120 Jul 17 15:57 cronjob_bandit22
-rw-r--r--   1 root root   122 Jul 17 15:57 cronjob_bandit23
-rw-r--r--   1 root root   120 Jul 17 15:57 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8 14:38 e2scrub_all
-rwx------   1 root root    52 Jul 17 15:59 otw-tmp-dir*
-rw-r--r--   1 root root   102 Mar 31 00:06 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

这里的 `* * * * *` 意思是每秒执行一次



## L22 -> L23 others script

> ## Level Goal
>
> A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.
>
> **NOTE:** Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.
>
> ## Commands you may need to solve this level
>
> cron, crontab, crontab(5) (use “man 5 crontab” to access this)



```
bandit22@bandit:/etc/cron.d$ ll
total 44
drwxr-xr-x   2 root root  4096 Jul 17 15:59 ./
drwxr-xr-x 121 root root 12288 Aug  1 14:49 ../
-rw-r--r--   1 root root   120 Jul 17 15:57 cronjob_bandit22
-rw-r--r--   1 root root   122 Jul 17 15:57 cronjob_bandit23
-rw-r--r--   1 root root   120 Jul 17 15:57 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8 14:38 e2scrub_all
-rwx------   1 root root    52 Jul 17 15:59 otw-tmp-dir*
-rw-r--r--   1 root root   102 Mar 31 00:06 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

是一段奇怪的代码，运行试试

```

bandit22@bandit:/etc/cron.d$ /usr/bin/cronjob_bandit23.sh
Copying passwordfile /etc/bandit_pass/bandit22 to /tmp/8169b67bd894ddbb4412f91573b38db3
bandit22@bandit:/etc/cron.d$ cat /tmp/8169b67bd894ddbb4412f91573b38db3
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

这里是把我自己的密码复制过去了，把用户名改一改，就可以找到了

```
bandit22@bandit:/etc/cron.d$ myname=bandit23
bandit22@bandit:/etc/cron.d$ mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)
bandit22@bandit:/etc/cron.d$ echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"
Copying passwordfile /etc/bandit_pass/bandit23 to /tmp/8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```



## L23 -> L24 run me

> ## Level Goal
>
> A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.
>
> **NOTE:** This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!
>
> **NOTE 2:** Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…
>
> ## Commands you may need to solve this level
>
> chmod, cron, crontab, crontab(5) (use “man 5 crontab” to access this)

```
bandit23@bandit:/etc/cron.d$ cat cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cron
cronjob_bandit22.sh  cronjob_bandit23.sh  cronjob_bandit24.sh  crontab
bandit23@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

看起来如果 owner 是我们的话，就会执行我们的脚本

```shell
$ mktemp -d
/tmp/tmp.SwuGSBGO3O
$ cd /tmp/tmp.SwuGSBGO3O
$ vim a.sh
```

```shell
#/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/tmp.SwuGSBGO3O/pass
```

```shell

$ chmod +x a.sh
$ cp a.sh /var/spool/bandit24/foo/
# 等待 60 秒
$ cat pass
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```



## L24->L25 brute-forcing

> ## Level Goal
>
> A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
> You do not need to create new connections each time

爆破 4 位数密码

```shell
$ mktemp -d
/tmp/tmp.vNMx0vk8LD
$ cd /tmp/tmp.vNMx0vk8LD
$ vim a.py
```



```python
#!/bin/python3
import socket
import threading

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

server_address = ('localhost', 30002)
client_socket.connect(server_address)
output = open("/tmp/tmp.vNMx0vk8LD/LOG.log", "wb")

def recv():
    # 接收 Hello
    while True:
        try:
        	response = client_socket.recv(1)
            output.write(response)
        	#print(response.decode(), end="")
        except:
            pass
    
threading.Thread(target=recv).start()
    
try:
    # 发送 Hi
    input("Enter to start")
    message = 'gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 %04d\n'
    for i in range(10000):
        sendstr = message % i
    	client_socket.sendall(sendstr.encode())
    input()

finally:
    # 关闭连接
    client_socket.close()
    output.close()
```



```shell
$ cat LOG.log | grep -v "Wrong"
I am the pincode checker for user bandit25. Please enter the password for user bandit24 and the secret pincode on a single line, separated by a space.
Correct!
The password of user bandit25 is iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
```



## L25 -> L26 "more" excape

> 密码：iCi86ttT4KSNe1armKiwbQNmB3YJP3q4
>
> ## Level Goal
>
> Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not **/bin/bash**, but something else. Find out what it is, how it works and how to break out of it.
>
> > NOTE: if you’re a Windows user and typically use Powershell to `ssh` into bandit: Powershell is known to cause issues with the intended solution to this level. You should use command prompt instead.
>
> ## Commands you may need to solve this level
>
> ssh, cat, more, vi, ls, id, pwd



```shell
$ cat bandit26.sshkey
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEApis2AuoooEqeYWamtwX2k5z9uU1Afl2F8VyXQqbv/LTrIwdW
pTfaeRHXzr0Y0a5Oe3GB/+W2+PReif+bPZlzTY1XFwpk+DiHk1kmL0moEW8HJuT9
/5XbnpjSzn0eEAfFax2OcopjrzVqdBJQerkj0puv3UXY07AskgkyD5XepwGAlJOG
xZsMq1oZqQ0W29aBtfykuGie2bxroRjuAPrYM4o3MMmtlNE5fC4G9Ihq0eq73MDi
1ze6d2jIGce873qxn308BA2qhRPJNEbnPev5gI+5tU+UxebW8KLbk0EhoXB953Ix
3lgOIrT9Y6skRjsMSFmC6WN/O7ovu8QzGqxdywIDAQABAoIBAAaXoETtVT9GtpHW
qLaKHgYtLEO1tOFOhInWyolyZgL4inuRRva3CIvVEWK6TcnDyIlNL4MfcerehwGi
il4fQFvLR7E6UFcopvhJiSJHIcvPQ9FfNFR3dYcNOQ/IFvE73bEqMwSISPwiel6w
e1DjF3C7jHaS1s9PJfWFN982aublL/yLbJP+ou3ifdljS7QzjWZA8NRiMwmBGPIh
Yq8weR3jIVQl3ndEYxO7Cr/wXXebZwlP6CPZb67rBy0jg+366mxQbDZIwZYEaUME
zY5izFclr/kKj4s7NTRkC76Yx+rTNP5+BX+JT+rgz5aoQq8ghMw43NYwxjXym/MX
c8X8g0ECgYEA1crBUAR1gSkM+5mGjjoFLJKrFP+IhUHFh25qGI4Dcxxh1f3M53le
wF1rkp5SJnHRFm9IW3gM1JoF0PQxI5aXHRGHphwPeKnsQ/xQBRWCeYpqTme9amJV
tD3aDHkpIhYxkNxqol5gDCAt6tdFSxqPaNfdfsfaAOXiKGrQESUjIBcCgYEAxvmI
2ROJsBXaiM4Iyg9hUpjZIn8TW2UlH76pojFG6/KBd1NcnW3fu0ZUU790wAu7QbbU
i7pieeqCqSYcZsmkhnOvbdx54A6NNCR2btc+si6pDOe1jdsGdXISDRHFb9QxjZCj
6xzWMNvb5n1yUb9w9nfN1PZzATfUsOV+Fy8CbG0CgYEAifkTLwfhqZyLk2huTSWm
pzB0ltWfDpj22MNqVzR3h3d+sHLeJVjPzIe9396rF8KGdNsWsGlWpnJMZKDjgZsz
JQBmMc6UMYRARVP1dIKANN4eY0FSHfEebHcqXLho0mXOUTXe37DWfZza5V9Oify3
JquBd8uUptW1Ue41H4t/ErsCgYEArc5FYtF1QXIlfcDz3oUGz16itUZpgzlb71nd
1cbTm8EupCwWR5I1j+IEQU+JTUQyI1nwWcnKwZI+5kBbKNJUu/mLsRyY/UXYxEZh
ibrNklm94373kV1US/0DlZUDcQba7jz9Yp/C3dT/RlwoIw5mP3UxQCizFspNKOSe
euPeaxUCgYEAntklXwBbokgdDup/u/3ms5Lb/bm22zDOCg2HrlWQCqKEkWkAO6R5
/Wwyqhp/wTl8VXjxWo+W+DmewGdPHGQQ5fFdqgpuQpGUq24YZS8m66v5ANBwd76t
IZdtF5HXs2S5CADTwniUS5mX1HO9l5gUkk+h0cH5JnPtsMCnAUM+BRY=
-----END RSA PRIVATE KEY-----
# 保存到本地
$ chmod 600 bandit26.sshkey
$ ssh bandit26@bandit.labs.overthewire.org -p 2220 -i bandit26.sshkey
```

一登录进去就退出来了？如果输入命令（任意），就会不退出，但是啥都干不了，如果按 `Ctrl+D` 就会有东西出现

```shell
$ ssh bandit26@bandit.labs.overthewire.org -p 2220 -i L26.private_key pwd
                         _                     _ _ _
                        | |__   __ _ _ __   __| (_) |_
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_
                        |_.__/ \__,_|_| |_|\__,_|_|\__|


                      This is an OverTheWire game server.
            More information on http://www.overthewire.org/wargames

::::::::::::::
/home/bandit26/text.txt
::::::::::::::
  _                     _ _ _   ___   __
 | |                   | (_) | |__ \ / /
 | |__   __ _ _ __   __| |_| |_   ) / /_
 | '_ \ / _` | '_ \ / _` | | __| / / '_ \
 | |_) | (_| | | | | (_| | | |_ / /| (_) |
 |_.__/ \__,_|_| |_|\__,_|_|\__|____\___/
```



好家伙，可以 ls 看到 26 的 home

```
bandit25@bandit:~$ ll /home/bandit26/
total 44
drwxr-xr-x  3 root     root      4096 Jul 17 15:57 ./
drwxr-xr-x 70 root     root      4096 Jul 17 15:58 ../
-rwsr-x---  1 bandit27 bandit26 14880 Jul 17 15:57 bandit27-do*
-rw-r--r--  1 root     root       220 Mar 31 08:41 .bash_logout
-rw-r--r--  1 root     root      3771 Mar 31 08:41 .bashrc
-rw-r--r--  1 root     root       807 Mar 31 08:41 .profile
drwxr-xr-x  2 root     root      4096 Jul 17 15:57 .ssh/
-rw-r-----  1 bandit26 bandit26   258 Jul 17 15:57 text.txt
```

搜了下，`cat /etc/passwd` 可以看到其他用户使用的 shell

```shell
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
```

要开始打 pwn 了？

```shell
$ scp -P 2220 bandit25@bandit.labs.overthewire.org:/usr/bin/showtext ./
```

哦哦，是个 shell script

```shell
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
```

> exec 命令用来执行一个可执行文件，并用该文件替换当前 shell 进程。

好家伙，用 more 的话，只要显示完，就会自动退出，所以只要把窗口拉的很小，就可以在 more 暂停

<img src="./assets/image-20240909163757186.png" alt="image-20240909163757186" style="zoom:50%;" />

然后就可以开始操作了。

这是 `man more`， SHELL 和 TERM 的变量会对 more 有影响。

![image-20240909163902597](./assets/image-20240909163902597.png)

按 `v` 就可以进 vim，然后 `:e /etc/bandit_pass/bandit26` 即可看到密码

```shell
s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ
```

但是没用，登进去了也是个 more，好奇这是怎么做到的

看了 [writeup ](https://mayadevbe.me/posts/overthewire/bandit/level26/) ，知道 vim 可以设置 shell：

```shell
:set shell=/bin/bash
```





## L26->L27 just do

> 密码：s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ 但是没用，登进去了也是个 more
>
> ## Level Goal
>
> Good job getting a shell! Now hurry and grab the password for bandit27!
>
> ## Commands you may need to solve this level
>
> ls

```shell
$ ./bandit27-do cat /etc/bandit_pass/bandit27
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```



## L27 -> L28 easy git

> 密码：upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
>
> ## Level Goal
>
> There is a git repository at `ssh://bandit27-git@localhost/home/bandit27-git/repo` via the port `2220`. The password for the user `bandit27-git` is the same as for the user `bandit27`.
>
> Clone the repository and find the password for the next level.
>
> ## Commands you may need to solve this level
>
> git

```shell
$ mktemp -d
/tmp/tmp.T2R4tf7QrB
$ git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
$ cd repo
$ cat README
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```



## L28 -> L 29 git privacy leak

> Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
>
> ## Level Goal
>
> There is a git repository at `ssh://bandit28-git@localhost/home/bandit28-git/repo` via the port `2220`. The password for the user `bandit28-git` is the same as for the user `bandit28`.
>
> Clone the repository and find the password for the next level.
>
> ## Commands you may need to solve this level
>
> git

```shell
$ cd $(mktemp -d)
/tmp/tmp.uGeRlR20OG
$ git clone ssh://bandit28-git@localhost:2220/home/bandit28-git/repo
$ git log
commit 8cbd1e08d1879415541ba19ddee3579e80e3f61a (HEAD -> master, origin/master, origin/HEAD)
Author: Morla Porla <morla@overthewire.org>
Date:   Wed Jul 17 15:57:30 2024 +0000

    fix info leak

commit 73f5d0435070c8922da12177dc93f40b2285e22a
Author: Morla Porla <morla@overthewire.org>
Date:   Wed Jul 17 15:57:30 2024 +0000

    add missing data

commit 5f7265568c7b503b276ec20f677b68c92b43b712
Author: Ben Dover <noone@overthewire.org>
Date:   Wed Jul 17 15:57:30 2024 +0000

    initial commit of README.md
$ git checkout -b tmp
$ git reset --hard HEAD^
$ cat README.md
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```



## L29 -> L30 git branch

> 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
>
> ## Level Goal
>
> There is a git repository at `ssh://bandit29-git@localhost/home/bandit29-git/repo` via the port `2220`. The password for the user `bandit29-git` is the same as for the user `bandit29`.
>
> Clone the repository and find the password for the next level.
>
> ## Commands you may need to solve this level
>
> git

```shell
$ mktemp -d
/tmp/tmp.49OLnRknIk
$ git clone ssh://bandit29-git@localhost:2220/home/bandit29-git/repo
```



```shell
$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>
```

会不会有“开发”分支？

```shell
$ cat ./.git/packed-refs
# pack-refs with: peeled fully-peeled sorted
eef534022d1ecc3b41d6de068501c4db4154b2c7 refs/remotes/origin/dev
efa5bd803f8335e5e5e9da5c4c7c876aefc9f8b4 refs/remotes/origin/master
471d89e684d9a74e532ddec73746e388f24d20a7 refs/remotes/origin/sploits-dev
```

还真有

```shell
$ git checkout dev
branch 'dev' set up to track 'origin/dev'.
Switched to a new branch 'dev'
$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```



其实，这样就能找到分支

```shell
$ git branch --all
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev
```



## L30 -> L31 what's blob

> qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
>
> ## Level Goal
>
> There is a git repository at `ssh://bandit30-git@localhost/home/bandit30-git/repo` via the port `2220`. The password for the user `bandit30-git` is the same as for the user `bandit30`.
>
> Clone the repository and find the password for the next level.
>
> ## Commands you may need to solve this level
>
> git

```shell
/tmp/tmp.XF9Jx3fMHK
$ git clone ssh://bandit30-git@localhost:2220/home/bandit30-git/repo
```

不得不说 packed-refs 还挺不错

```shell
$ cat ./.git/packed-refs
# pack-refs with: peeled fully-peeled sorted
60410f42e05023128098dc1f6991c75e6ae02e47 refs/remotes/origin/master
84368f3a7ee06ac993ed579e34b8bd144afad351 refs/tags/secret
```

```shell
$ git reset --hard 84368f3a7ee06ac993ed579e34b8bd144afad351
error: object 84368f3a7ee06ac993ed579e34b8bd144afad351 is a blob, not a commit
fatal: Could not parse object '84368f3a7ee06ac993ed579e34b8bd144afad351'.
```

被提示这是一个 blob，不是一个 commit

```shell
$ git cat-file blob 84368f3a7ee06ac993ed579e34b8bd144afad351
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```



参考：

- [Introducing the blob | Git from the Bottom Up (jwiegley.github.io)](https://jwiegley.github.io/git-from-the-bottom-up/1-Repository/2-introducing-the-blob.html)

- [A Visual Guide to Git Internals — Objects, Branches, and How to Create a Repo From Scratch (freecodecamp.org)](https://www.freecodecamp.org/news/git-internals-objects-branches-create-repo/)



## L31 -> L32 gitignore

> fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
>
> ## Level Goal
>
> There is a git repository at `ssh://bandit31-git@localhost/home/bandit31-git/repo` via the port `2220`. The password for the user `bandit31-git` is the same as for the user `bandit31`.
>
> Clone the repository and find the password for the next level.
>
> ## Commands you may need to solve this level
>
> git

```shell
/tmp/tmp.WM8a4nwjGN
$ git clone ssh://bandit31-git@localhost:2220/home/bandit31-git/repo
$ ll
total 20
drwxrwxr-x 3 bandit31 bandit31 4096 Sep 10 07:31 ./
drwx------ 3 bandit31 bandit31 4096 Sep 10 07:30 ../
drwxrwxr-x 8 bandit31 bandit31 4096 Sep 10 07:31 .git/
-rw-rw-r-- 1 bandit31 bandit31    6 Sep 10 07:31 .gitignore
-rw-rw-r-- 1 bandit31 bandit31  147 Sep 10 07:31 README.md
$ cat .gitignore
*.txt
$ cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master
```

ignore 掉了，删掉 ignore 就能推送

```shell
$ git push
...
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
remote:
remote: Well done! Here is the password for the next level:
remote: 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
remote:
remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
...
```



## L32 -> L33

> 3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K
>
> ## Level Goal
>
> After all this `git` stuff, it’s time for another escape. Good luck!
>
> ## Commands you may need to solve this level
>
> sh, man

```shell
WELCOME TO THE UPPERCASE SHELL
>>
```

用上一个号，看看用的什么shell

```shell
$ cat /etc/passwd | grep bandit32
bandit32:x:11032:11032:bandit level 32:/home/bandit32:/home/bandit32/uppershell
```

权限是 `-rwsr-x---` ，没法子看

好家伙，用之前的 shell 逃逸就出了

```shell
>> $0
$ whoami
bandit33
$ cat /etc/bandit_pass/bandit33
tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0
```



## L33

完结撒花~

```shell
$ cat README.txt
Congratulations on solving the last level of this game!

At this moment, there are no more levels to play in this game. However, we are constantly working
on new levels and will most likely expand this game with more levels soon.
Keep an eye out for an announcement on our usual communication channels!
In the meantime, you could play some of our other wargames.

If you have an idea for an awesome new level, please let us know!
```

