# Commands Practiced

## 1. Linux Navigation & File System

### `pwd`

Shows the current working directory.

```bash
pwd
```

I used it to confirm where I was working in the Linux filesystem.

### `ls`

Lists files and directories in the current location.

```bash
ls
```

### `ls -la`

Lists files and directories, including hidden files, with detailed information.

```bash
ls -la
```

I used this to inspect the contents and permissions of my home directory.

### `cd`

Changes the current working directory.

```bash
cd /home/ec2-user
```

I used it to move between directories while exploring the Linux filesystem.



## 2. AWS EC2 & SSH

### EC2 Instance

I launched an Amazon EC2 instance using Amazon Linux 2023. I used a `t3.micro` instance and enabled a public IP so that I could connect to it remotely.

The instance was placed in a VPC and protected by a Security Group that allowed SSH access from my IP address.

### SSH

I connected to the EC2 instance from my local Linux terminal using SSH.

```bash
ssh -i week1-ec2-key.pem ec2-user@<public-ip>
```

SSH provided a secure remote connection between my computer and the EC2 Linux server.

### `whoami`

```bash
whoami
```

Output:

```text
ec2-user
```

I used this to confirm which Linux user I was logged in as.

### `hostname`

```bash
hostname
```

I used this to identify the hostname of the EC2 instance.

### `pwd`

```bash
pwd
```

Output:

```text
/home/ec2-user
```

This confirmed that I was working from the `ec2-user` home directory.

### What I Learned

This lab helped me connect the cloud concepts I learned in AWS with an actual Linux server. I learned that EC2 provides the virtual server, while SSH allows me to remotely access and work on that server.


## 3. Users, Groups & Permissions

### `id`

Shows information about a user, including their UID, GID, and groups.

```bash
id
```

I used it to see which user I was logged in as and which groups the user belonged to.

### Create a User

I created a new Linux user called `dev1`.

```bash
sudo adduser -m dev1
```

I then checked the user's information:

```bash
id dev1
```

This showed the user's UID, GID, and group membership.

### Create a Group

I created a group called `dev`.

```bash
sudo groupadd dev
```

I initially tried `addgroup`, but Amazon Linux did not have that command available. I learned that `groupadd` was the appropriate command on this system.

### Add a User to a Group

```bash
sudo usermod -aG dev dev1
```

I used `-aG` to add `dev1` to the `dev` group without removing the user's existing group memberships.

I verified the change with:

```bash
id dev1
```

### `chown`

I changed the group ownership of a file:

```bash
sudo chown :dev file.txt
```

The `:dev` means that only the group ownership was changed. The file owner remained `ec2-user`.

### `chmod`

I practiced changing file permissions using `chmod`.

For example:

```bash
chmod u-w file.txt
```

This removes write permission from the file owner.

I also used:

```bash
sudo chmod g+w file.txt
```

This gave the group write permission.

### Testing Group Permissions

After adding `dev1` to the `dev` group and giving the group write permission, I switched to `dev1` and tested writing to the file:

```bash
echo "written by dev1" >> file.txt
```

The command succeeded because `dev1` was a member of the `dev` group and the group had write permission.

### What I Learned

I learned that file access is controlled separately for the owner, group, and others. A user does not have to own a file to modify it; they can access it through the permissions granted to a group they belong to.


## 4. File Archiving with `tar`

I practiced using `tar` to create an archive, view its contents, and extract the files from it.

### Create an Archive

```bash
tar -cvf test-lab.tar file.txt
```

What the options mean:

* `-c` — create a new archive
* `-v` — show the files being processed
* `-f` — specify the archive filename

This created `test-lab.tar` containing `file.txt`.

### List Archive Contents

```bash
tar -tvf test-lab.tar
```

I used this to view what was stored inside the archive without extracting it.

### Extract an Archive

First, I created a separate directory:

```bash
mkdir extracted
```

Then I extracted the archive into it:

```bash
tar -xvf test-lab.tar -C extracted
```

* `-x` — extract
* `-v` — show the files being extracted
* `-f` — specify the archive
* `-C` — extract into the specified directory

I then checked the extracted files with:

```bash
ls -l extracted
```

### What I Learned

I learned that `tar` is used to package files into an archive and later extract them. I also learned that the `.tar` extension is a naming convention for an uncompressed tar archive; the archive itself is different from a compressed `.tar.gz` archive.




## 5. Pipes, `grep` & `tee`

### Pipe `|`

A pipe sends the output of one command as input to another command.

For example:

```bash
ls -l | grep file
```

I used this to list files and then filter the output to show entries containing `file`.

### `grep`

`grep` searches command output or files for matching text.

For example:

```bash
ls -l | grep txt
```

I used it to filter the output and find entries containing `txt`.

### `tee`

`tee` displays command output on the terminal while also saving that output to a file.

```bash
ls -l | tee listing.txt
```

I then checked the saved output:

```bash
cat listing.txt
```

### Combining Commands

I combined `ps`, `grep`, and `tee` while working with Linux processes:

```bash
ps aux | grep sshd | tee sshd.txt
```

This:

1. Listed running processes with `ps aux`
2. Filtered the output for `sshd` using `grep`
3. Displayed the result and saved it to `sshd.txt` using `tee`

I also noticed that `grep` and `tee` could appear in the output because their own command lines contained the text being searched.

### What I Learned

I learned that Linux commands can be connected together to create a useful workflow. Instead of running each command separately, I can use pipes to pass output between commands and `tee` when I want to both see and save the result.


## 6. Processes & Process Management

A process is a running instance of a program or command. Linux assigns each process a unique Process ID (PID).

### `ps`

```bash
ps
```

I used `ps` to view processes associated with my current terminal session.

### `ps aux`

```bash
ps aux
```

I used this to view processes running across the system.

Some important columns include:

* `USER` — user running the process
* `PID` — Process ID
* `%CPU` — CPU usage
* `%MEM` — memory usage
* `STAT` — process state
* `COMMAND` — command that started the process

### Finding a Process with `grep`

```bash
ps aux | grep sshd
```

I used this to find SSH-related processes.

I initially tried:

```bash
ps aux | sshd
```

This was incorrect because the pipe sends the output to `sshd` as a command instead of searching for the text. I learned that `grep` is what I should use when I want to filter command output.

### `jobs`

I started a background process:

```bash
sleep 600 &
```

Then checked my background jobs:

```bash
jobs
```

This showed the `sleep` process running in the background.

### Checking a Process by PID

I used `ps -p` to check a specific process:

```bash
ps -p <PID>
```

This allowed me to verify whether the process was still running.

### `kill`

I terminated a process using its PID:

```bash
kill <PID>
```

This sends the normal termination signal (`SIGTERM`).

### Stopping and Resuming a Process

I started another background process and temporarily stopped it:

```bash
sleep 500 &
kill -STOP <PID>
```

I then resumed it:

```bash
kill -CONT <PID>
```

`SIGSTOP` pauses a process, while `SIGCONT` allows a stopped process to continue.

### Force Termination

I also practiced:

```bash
kill -9 <PID>
```

This sends `SIGKILL`, which forcefully terminates a process.

### What I Learned

I learned that processes have PIDs that can be used to monitor and control them. I also learned the difference between terminating a process normally, forcefully terminating it, stopping it temporarily, and continuing a stopped process.

I learned to first identify and check a PID before sending a signal rather than killing processes blindly.


## 7. System Monitoring

I practiced several Linux commands for checking system resources and disk usage.

### `top`

```bash
top
```

I used `top` to monitor the system in real time.

It showed information about:

* Running processes
* CPU usage
* Memory usage
* System load
* Process IDs and resource usage

I observed that the EC2 instance had very low CPU usage while I was not running resource-intensive tasks.

I exited `top` by pressing:

```text
q
```

### `df -h`

```bash
df -h
```

I used this to check available and used space on the filesystem.

The `-h` option displays the sizes in a human-readable format.

My EC2 instance had an 8 GB root filesystem, with most of the space still available.

### `du -sh`

```bash
du -sh .
```

I used this to check the total disk usage of my current directory.

* `-s` — show a summary
* `-h` — human-readable format
* `.` — current directory

### `du -h`

```bash
du -h .
```

Unlike `du -sh`, this showed the disk usage of the directory and its subdirectories.

### What I Learned

I learned the difference between checking overall filesystem space and checking how much space individual directories are using.

`df` helps me understand available filesystem space, while `du` helps me find where disk space is being used. `top` gives me a real-time view of processes and system resources.


## 8. Linux Services & `systemctl`

Linux services are background programs managed by the operating system. I practiced checking services using `systemctl`.

### Check a Service

I checked the SSH service:

```bash
systemctl status sshd
```

The output showed that the service was:

```text
active (running)
```

This confirmed that the SSH service was currently running on the EC2 instance.

### Check if a Service Starts at Boot

```bash
systemctl is-enabled sshd
```

The output was:

```text
enabled
```

This means the SSH service is configured to start automatically when the system boots.

### Difference Between `active` and `enabled`

I learned that these describe two different things:

* `active` — the service is running now
* `enabled` — the service is configured to start automatically when the system boots

A service can therefore be enabled without currently running, or running without being enabled to start automatically.

### List Running Services

```bash
systemctl list-units --type=service --state=running
```

I used this to view services currently running on the EC2 instance.

Some services I observed included:

* `sshd` — handles SSH connections
* `amazon-ssm-agent` — AWS Systems Manager agent
* `chronyd` — time synchronization
* `auditd` — system auditing
* `systemd-journald` — system logging

### What I Learned

I learned that Linux services run in the background to provide different system functions. `systemctl` can be used to inspect and manage these services.

I also learned to be careful when managing services on a remote server. Since I was connected to the EC2 instance through SSH, stopping the SSH service could disconnect my session.
