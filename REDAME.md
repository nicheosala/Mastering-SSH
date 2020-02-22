# Mastering SSH

---

**WARNING**\
You cannot copy-paste the configs as they are. **First, you have to generate an ed25519 key in order to use them as they are**.
1. On the client: ssh-keygen -t ed25519 (this is used to generate the key)
2. On the client: ssh-copy-id USERNAME@HOSTNAME (connect to your server, copying the newly generated key)
3. Try again to log into the server using the key, then do whatever you want.

---

---

**WARNING**\
These are drafts. One day I'll arrange them. In the meanwhile, if you want to help me or if you have some tips to harden the SSH configuration, please tell me! I need to know people like you.

---

I'm configuring my first personal server. I will access it only through SSH. It is *fondamentale* to guarantee a robust configuration. So, I decided to write this guide, that will be a reference for myself and a lesson for other, I hope.

See the configs I wrote in order to reach the most secure SSH configuration possible!

## Best practices

The file '/etc/ssh/sshd_config' is the configuration file for the SSH server. Every instruction has a comment the describe its reason in details.

Before modifying any configuration files, you should do a backup. For example:
'cp /etc/ssh/sshd_config /etc/ssh/backup.sshd_config'

After modyfing any configuration files (and before applying the new modifications), you should test the correctness of that file. For example:
'sudo /usr/sbin/sshd -t'

In the end, to reload the configuration file in order to apply the changes: 'sudo systemctl reload sshd'

## Add the SSH users to ssh-user group 
I decided to disable whitelist users and use whitelist groups. Why? When deleting a user from the system, the username is not removed from sshd_config, which adds to maintenance requirements. The solution is to use the AllowGroups setting instead, and add users to an ssh-user group. Moreover, I don't have to write my username in this document, making it more general. Create the ssh-user group with 'sudo groupadd ssh-user', then add each ssh user to the group with 'sudo usermod -a -G ssh-user <username>'.

## Audit your configuration
You can use ssh-audit.py in order to audit your new configuration.
Download it using git:
'git clone https://github.com/arthepsy/ssh-audit.git'
Move into the directory just donloaded:
'cd ssh-audit'
Execute the script:
```
python3 ssh-audit.py [- p PORT] HOSTNAME
```

## Update OpenSSH
Usually, the version of OpenSSH into the PPA is far from the newest one (always for compatibility reasons!). But we want to have the latest version. How?
You can find detailed instructions on the OpenSSH website. OpenSSH is developed for BSD operating systems, then it is ported to unix systems like Linux. For these reason, the OpenSSH version for Linux is called "OpenSSH portable". Follow the [official instructions](https://www.openssh.com/portable.html#downloads)
in order to upgrade your OpenSSH installation. We will follow these instructions referring using GitHub in order to get the files and update everything. Start!
'git clone https://github.com/openssh/openssh-portable' (if Git is not installed, use 'sudo apt install git'). This will download the official OpenSSH repository on GitHub, that contains all the files we need.
'''
cd openssh-portable
autoreconf
./configure
make && make tests
make install
'''
You should have to install 'autoconf' via 'sudo apt install autoconf' in order to use 'autoreconf' command.
When you execute 'configure', probably you'll get an error about zlib. It is a dependency of OpenSSH (as explained in the very good official guide). We can install it through PPA using 'sudo apt install zlib1g-dev'. Other dependencies needed: gcc, libssl-dev
The 'make tests' command needs a long time to complete. It verifies that all is ready for our upgrade. 

## Sources
- [Hardening SSH](https://medium.com/@jasonrigden/hardening-ssh-1bcb99cd4cef)
- [SSH audit](https://github.com/arthepsy/ssh-audit)
- [Secure secure shell](https://stribika.github.io/2015/01/04/secure-secure-shell.html)
- [Mozilla guidelines](https://infosec.mozilla.org/guidelines/openssh)
- [Understanding SSH](https://www.digitalocean.com/community/tutorials/understanding-the-ssh-encryption-and-connection-process)