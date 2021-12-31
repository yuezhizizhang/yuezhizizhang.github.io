---
layout: post
title:  "Git Stopped Working on Windows 10: Permission denied (publickey)"
categories: "git ssh"
---

10 days ago, Git suddenly stopped working on my Windows 10. Every time I ran a Git command to work with remote repository, e.g. <code class="code">git pull origin master</code>, Git failed saying **Permision denied (publickey).**.

![Cascading Selector](/assets/2021-12-31-git-ssh-stopped-working-on-windows10.png "git pull permission denied")

At first, I thought something must be going wrong with my SSH key. So I followed instructions from [Set up an SSH key](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/){:target="_blank"}, re-generated a new key and added the public key to my Bitbucket account. It didn't work. I tried several times but it wouldn't help.

Later, someone from my team said he has met the same problems weeks ago. In the end, he has to switch to **HTTPS**. To reset the origin for the local repository to HTTPs:

```
git remote set-url origin https://stash.XXX.git
```

Bitbucket has a document [Permanently authenticating with Git repositories](https://confluence.atlassian.com/bitbucketserver/permanently-authenticating-with-git-repositories-776639846.html){:target="_blank"} explaining how to authenticate with credential helper. I didn't try it personally, because I still wanted to figure out why SSH key authentication suddenly failed on my machine.

Finally, another colleague unfolded the mystery. There are two sets of SSH clients/agents on Windows 10. One is native to Windows, and the other is shipped with Git Bash.

![Cascading Selector](/assets/2021-12-31-where-ssh.png "where ssh")

When I ran <code class="code">ssh-add ~/.ssh/{private_key_file}</code> in Git Bash to add the key to the ssh-agent, I only added the key to the SSH agent shipped with Git Bash. For some reason, it's not enough.

Eventually, I found a blog [Using the OpenSSH client included in Windows 10 (1809) as your Git’s SSH client](https://poshsecurity.com/blog/using-the-openssh-client-included-in-windows-10-1809-as-your-gits-ssh-client){:target="_blank"} explaining how to configure Git to use OpenSSH for Windows.

Briefly speaking, firstly, use the git config to specify the OpenSSH client.

```cmd
git config --global core.sshcommand "C:/Windows/System32/OpenSSH/ssh.exe"
```

Next, find **"OpenSSH Authentication Agent"** in Windows service and change its Startup type to **Manual**.

![Cascading Selector](/assets/2021-12-31-openssh-agent.png "openssh agent")

Done. Now, when I set the origin of my Git repository back to SSH connection, it's working again!