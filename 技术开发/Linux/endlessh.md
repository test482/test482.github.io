# endlessh

[https://aur.archlinux.org/packages/endlessh-git](https://aur.archlinux.org/packages/endlessh-git)

[https://github.com/skeeto/endlessh](https://github.com/skeeto/endlessh)

配置

```bash
$ cat /etc/endlessh.conf
# The port on which to listen for new SSH connections.
Port 22

# The endless banner is sent one line at a time. This is the delay
# in milliseconds between individual lines.
Delay 10000

# The length of each line is randomized. This controls the maximum
# length of each line. Shorter lines may keep clients on for longer if
# they give up after a certain number of bytes.
MaxLineLength 32

# Maximum number of connections to accept at a time. Connections beyond
# this are not immediately rejected, but will wait in the queue.
MaxClients 4096

# Set the detail level for the log.
#   0 = Quiet
#   1 = Standard, useful log messages
#   2 = Very noisy debugging information
LogLevel 1
```
