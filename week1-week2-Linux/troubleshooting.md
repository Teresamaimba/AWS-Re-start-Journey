# Troubleshooting

## 1. Lost EC2 Private Key

### Problem

I initially launched an EC2 instance using a key pair, but I could not find the `.pem` private key on my computer.

### What I Learned

AWS does not allow the original private key to be downloaded again after the key pair is created. Without the private key, I could not use SSH to authenticate to that instance.

I terminated the test instance and created a new key pair, making sure to save the `.pem` file securely before launching the replacement instance.

### Lesson

Private keys are required for SSH authentication and should be stored securely. They should never be uploaded to GitHub.

---

## 2. `addgroup` Command Not Found

### Problem

While creating a Linux group, I tried:

```bash
sudo addgroup dev
```

Amazon Linux returned:

```text
command not found
```

### Solution

I used:

```bash
sudo groupadd dev
```

The group was created successfully.

### Lesson

Linux commands can differ between distributions. A command I had encountered before was not available on Amazon Linux, so I had to identify the appropriate command for the operating system I was using.

---

## What Troubleshooting Taught Me

These errors helped me understand Linux through practical problems rather than memorizing commands. I learned to read error messages, check permissions and ownership, verify process IDs, and choose commands based on what I was actually trying to do.
---

## 3. Permission Denied When Creating a File

### Problem

I removed write permission from the directory and then tried to create a new file inside it.

```bash
touch new.txt
```

The command returned:

```text
Permission denied
```

### What I Learned

The write permission on a directory controls whether I can create or delete entries inside that directory.

This helped me understand that directory permissions and file permissions control different things.

---

## 4. `dev1` Could Not Modify the File

### Problem

I switched to the `dev1` user and tried to modify a file owned by `ec2-user`.

At first, the operation was denied because the dev group did not have write permission on the file.

### Solution

I gave the `dev` group write permission:

```bash
sudo chmod g+w file.txt
```

Because `dev1` was a member of the `dev` group, the user could then write to the file.

### Lesson

A user does not necessarily need to own a file to modify it. Access can also come from group permissions.


## 5. Incorrect Use of the Pipe with `sshd`

### Problem

I tried:

```bash
ps aux | sshd
```

The system returned an error saying that `sshd` required an absolute path.

### What I Learned

The pipe `|` sends the output of one command to another command.

I had intended to **search** the output for `sshd`, but I was actually trying to run `sshd` as the next command.

### Solution

I used `grep` to search the output:

```bash
ps aux | grep sshd
```

This correctly filtered the process list for entries containing `sshd`.

### Lesson

A pipe connects commands, while `grep` is used to search or filter text.

---

## 6. Trying to Kill a Process That Had Already Ended

### Problem

I started a `sleep` process in the background and later tried to terminate it using its PID.

The system returned:

```text
No such process
```

### What I Learned

The process had already finished, so its PID no longer existed.

I learned that before sending a signal to a process, it is useful to check whether the process is still running.

For example:

```bash
ps -p <PID>
```

---

## 7. Using `SIGSTOP` as a Command

### Problem

I initially tried to run:

```bash
SIGSTOP <PID>
```

The shell returned:

```text
command not found
```

### Solution

I learned that `SIGSTOP` is a signal, not a command. I used:

```bash
kill -STOP <PID>
```

I then resumed the stopped process with:

```bash
kill -CONT <PID>
```

### Lesson

Linux signals can be sent to processes using the `kill` command. The `kill` command is not limited to terminating processes; it can also send different signals for stopping, continuing, or controlling processes.

---

## 8. `dev1` Was Not Allowed to Use `sudo`

### Problem

After switching to `dev1`, I tried using `sudo`, but the system reported that `dev1` was not in the sudoers file.

### What I Learned

Being a normal Linux user or belonging to a group does not automatically give a user administrative privileges.

I also learned to distinguish between **file permissions** and **sudo privileges**. They control different types of access.


