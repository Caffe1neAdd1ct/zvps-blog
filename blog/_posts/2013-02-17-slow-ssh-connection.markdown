---
layout: post
title:  "Slow SSH password connection prompt"
date:   2013-02-17 02:35:06
categories: guides linux ssh
---

<span><h3>Slow SSH Password Prompt</h3><strong>Just connect already...</strong></span>
<br><br>
<div class="well well-large">
    <p>
        Some times you get a VPS or Dedciated server which is extremely slow responding to an SSH connection attempt, so lets get that sped up!
    </p>
    <p>
        Find the following file on the server and open it using <code>vi</code>:<br>
        <code>vi /etc/ssh/sshd_config</code>
    </p>
    <p>
        Next find the following directives and change them to "no", make sure they are uncommented (don't have a # at the start of the line):
    </p>
    <ol>
        <li><code>UseDNS no</code></li>
        <li><code>GSSAPIAuthentication no</code></li>
    </ol>
    <p>
        Save the file by typing <code>:wq</code> followed by the return or enter key. Finally to enable these changes you need to restart SSHD by running one of the following commands:
    </p>
    <ul>
        <li>For CentOS or Fedora : <code>service sshd restart</code></li>
        <li>For Debian or Ubuntu : <code>service ssh restart</code></li>
    </ul>
    <p>
        Press <code>ctrl + d</code> to disconnect, then reconnect and the password prompt should be almost instant. Now instead of using password authentication we would highly recommend researching and implementing key pair authentication.
    </p>
</div>