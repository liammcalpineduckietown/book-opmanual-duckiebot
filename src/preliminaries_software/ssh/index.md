```{seo}
:description: SSH, or Secure Shell, is a protocol to remote control computers, which comes in handy in robotics too. We provide some useful shortcuts for Duckietown here.
:keywords: Duckietown, Duckiebot, SSH, secure shell, shortcuts
```

(secure-shell)=
# Secure shell (SSH)

```{needget}
* Time: 5 minutes.
* Basic knowledge of [SSH](https://en.wikipedia.org/wiki/Secure_Shell)
---
* Useful shortcuts for using SSH in Duckietown.
```

SSH, or Secure Shell, is a protocol to remote control computers, which comes in handy in robotics too. In this section we provide shortcuts useful in the context of Duckietown daily operations.

Note: in the future you will have to debug problems, and these problems might be harder to understand if you rely blindly on the shortcuts.

(ssh-aliases)=
## SSH aliases

Instead of using

    ssh duckie@![ROBOT].local

You can set up SSH so that you can use:

    ssh ![ROBOT]

During your init_sd_card process described later in the book, the command will automatically setup `~/.ssh/config`. 
If you are having trouble using it, you can follow the instructions below.

To manually create an SSH alias, create a host section in 
`~/.ssh/config` on your laptop with the following contents:

    Host ![ROBOT]
        User duckie
        Hostname ![ROBOT].local

Note that this does **not** let you do

    ping ![ROBOT]

You haven't created another hostname, just an alias for SSH.

However, you can use the alias with all the tools that rely on SSH, including `rsync` and `scp`.