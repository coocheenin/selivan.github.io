---
layout: post
title:  "Auto-mount sshfs protected by password with tcl expect."
tags: [ssh,sshfs,sftp,expect]
---

*Note: [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) is SSH File Transefer Protocol, don't confuse it with [FTPS](https://en.wikipedia.org/wiki/FTPS) - FTP over SSL.*

To mount remote SFTP directory, you can use [sshfs](https://github.com/libfuse/sshfs).

For security reasons it does not support getting password from command line or environment variables. Password should be entered manually from terminal, automation is possible only with ssh keys.

In real world you have to deal with external services that provide SFTP protected by password and are not going to change that for your convenience.

We can use [TCL Expect](https://core.tcl-lang.org/expect/index) tool to override that limitation. There is also [sshpass](https://sourceforge.net/projects/sshpass) program that specifically automates using of ssh-like programs with passwords, but I couldn't make it work with sshfs.

```tcl
#!/usr/bin/expect -d

set env(LC_ALL) "C"
set env(PS1) "shell:"
set timeout 30

set user "user"
set password "password"
set host "host"
set port "22"
set local_dir "/srv/sftp-mount"
set remove_dir "/pub"

spawn /bin/sh

expect "shell:"

send -- "sshfs $user@$host:$remove_dir $local_dir -p $port -o reconnect\r"

expect "password:"

send -- "$password\r"

sleep 1

expect "shell:"
```

First line(sha-bang) tells that this is `expect` script. `-d` option is great for debuging, it makes `expect` verbose about it's actions.

Programs output may depend on current locale, so it's better to explicitly set the default one with `LC_ALL` variable.

Shell prompt may be also different depending on local settings, so we explicitly override it with `PS1` variable.

Note, that we use `"\r"`, not `"\n"`: we are sending the carriage return, not the new line symbol. Sometimes they are interchangeable, but not always.

To make sshfs work, you should first add hostkey to `~/.ssh/known_hosts`:

```bash
ssh -o StrictHostKeyChecking=no -o -p $PORT $USER@$HOST echo added key
```

You may also add `-o StrictHostKeyChecking=no` to `sshfs` options, but that is not secure: it makes man-in-the-middle attacks possible.

And here is systemd unit to create this mount on system start:

```ini
[Unit]
Description=Mount sshfs for wb with password
After=network.target auditd.service
ConditionPathIsMountPoint=!/srv/sftp-mount

[Service]
Type=oneshot
RemainAfterExit=true

User=sftp-mount
Group=sftp-mount

ExecStart=/usr/local/bin/sftp-mount
ExecStop=/bin/fusermount -u /srv/sftp-mount

[Install]
WantedBy=multi-user.target
```

`ConditionPathIsMountPoint` prevents from trying to mount over already mounted directory. `RemainAfterExit=true` notifies systemd that service should be considered running after start script has stopped working.