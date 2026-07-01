Linux Basic to Advance level

*********************************** File Permission ************************************************
https://chatgpt.com/c/6a447242-e1ec-83e8-b250-91d2f4440c66


# Linux File Permissions – Complete Study Notes

## Learning Objectives

After completing this topic, you should be able to:

* Understand Linux permission architecture.
* Read and interpret `ls -l` output.
* Change permissions using `chmod`.
* Change ownership using `chown` and `chgrp`.
* Understand numeric (octal) permissions.
* Apply correct permissions in real DevOps scenarios.
* Explain Linux permissions confidently in interviews.

---

# 1. Linux Permission Architecture

Every file and directory in Linux has three permission categories.

```
Owner (u)    Group (g)    Others (o)
```

### Owner (u)

The user who created or owns the file.

Example:

```
venkatasaikirannekkanti
```

---

### Group (g)

Users belonging to the same Linux group.

Example:

```
staff
developers
docker
```

---

### Others (o)

Every other user in the system.

---

# 2. Permission Types

There are three permission types.

| Symbol | Name    | Value | File                   | Directory                  |
| ------ | ------- | ----: | ---------------------- | -------------------------- |
| r      | Read    |     4 | Read file contents     | List directory contents    |
| w      | Write   |     2 | Modify file            | Create/Delete/Rename files |
| x      | Execute |     1 | Execute program/script | Enter (traverse) directory |

---

# 3. Understanding `ls -l`

Command:

```bash
ls -l
```

Example:

```text
-rw-r--r-- 1 venkatasaikirannekkanti staff 241 Dec 29 2024 67.py
```

Breakdown:

| Field                   | Meaning              |
| ----------------------- | -------------------- |
| -                       | File type            |
| rw-r--r--               | Permissions          |
| 1                       | Number of hard links |
| venkatasaikirannekkanti | Owner                |
| staff                   | Group                |
| 241                     | File size (Bytes)    |
| Dec 29 2024             | Last modified        |
| 67.py                   | File name            |

---

# 4. File Types

| Symbol | Meaning          |
| ------ | ---------------- |
| -      | Regular file     |
| d      | Directory        |
| l      | Symbolic link    |
| c      | Character device |
| b      | Block device     |
| s      | Socket           |
| p      | Named pipe       |

Example:

```
-rw-r--r-- file.txt
drwxr-xr-x project
lrwxrwxrwx shortcut
```

---

# 5. Reading Permissions

Example:

```
-rwxr-xr--
```

Breakdown:

```
Owner   Group   Others

rwx     r-x     r--
```

Meaning

Owner

* Read
* Write
* Execute

Group

* Read
* Execute

Others

* Read only

---

# 6. Numeric Permission Values

```
Read = 4
Write = 2
Execute = 1
```

Examples

```
rwx = 7

4+2+1
```

```
rw- = 6

4+2
```

```
r-x = 5

4+1
```

```
r-- = 4
```

```
-wx = 3

2+1
```

```
-w- = 2
```

```
--x = 1
```

```
--- = 0
```

---

# 7. Common Permission Combinations

## 777

```
rwxrwxrwx
```

Everyone can

* Read
* Write
* Execute

Use only for temporary testing.

---

## 755

```
rwxr-xr-x
```

Owner

* Read
* Write
* Execute

Others

* Read
* Execute

Best for

* Scripts
* Applications
* Directories

---

## 775

```
rwxrwxr-x
```

Used in shared development teams.

---

## 744

```
rwxr--r--
```

Only owner can modify.

---

## 644

```
rw-r--r--
```

Regular files.

---

## 600

```
rw-------
```

Sensitive files.

Examples

* SSH private keys
* API keys
* Password files

---

## 700

```
rwx------
```

Private directories/scripts.

---

# 8. chmod

Changes file permissions.

Syntax

```bash
chmod PERMISSION file
```

Examples

Executable script

```bash
chmod 755 app.py
```

Regular text file

```bash
chmod 644 notes.txt
```

Private key

```bash
chmod 600 id_rsa
```

Private directory

```bash
chmod 700 .ssh
```

Recursively

```bash
chmod -R 755 project
```

---

# 9. Symbolic chmod

Give execute permission

```bash
chmod +x script.sh
```

Remove write permission

```bash
chmod g-w file.txt
```

Add read permission

```bash
chmod o+r file.txt
```

Owner gets all permissions

```bash
chmod u=rwx file.txt
```

Multiple changes

```bash
chmod u+x,g-w,o-r file.txt
```

---

# 10. chown

Changes owner.

Example

```bash
sudo chown alice file.txt
```

Owner becomes

```
alice
```

---

Owner and group

```bash
sudo chown alice:developers app.py
```

---

# 11. chgrp

Change group only.

```bash
sudo chgrp developers app.py
```

---

# 12. File vs Directory Permissions

## File

```
rw-r--r--
```

Owner

* Read
* Write

Others

* Read only

---

## Directory

```
drwxr-xr-x
```

Owner

* Enter directory
* Create files
* Delete files

Others

* Enter
* List files

Without Execute

```
drw-r--r--
```

You cannot

```
cd directory
```

Even if Read permission exists.

---

# 13. Special Permissions

## SUID

```
chmod 4755 file
```

Display

```
-rwsr-xr-x
```

Program executes as the owner.

Example

```
passwd
```

---

## SGID

```
chmod 2755 project
```

Display

```
drwxr-sr-x
```

New files inherit the directory's group.

---

## Sticky Bit

```
chmod 1777 shared
```

Display

```
drwxrwxrwt
```

Only the file owner can delete their own files.

Example

```
/tmp
```

---

# 14. umask

Shows default permission mask.

```bash
umask
```

Example

```
0022
```

Default permissions

Files

```
666 - 022 = 644
```

Directories

```
777 - 022 = 755
```

---

# 15. Practical Lab

Create workspace

```bash
mkdir linux-lab
cd linux-lab
```

Create files

```bash
touch app.py
touch secrets.txt
mkdir scripts
```

Check permissions

```bash
ls -l
```

Make executable

```bash
chmod +x app.py
```

Secure secret

```bash
chmod 600 secrets.txt
```

Private directory

```bash
chmod 700 scripts
```

Verify

```bash
ls -l
```

---

# 16. Real DevOps Examples

| Scenario              | Permission |
| --------------------- | ---------: |
| SSH private key       |        600 |
| `.ssh` directory      |        700 |
| Shell script          |        755 |
| Python script         |        755 |
| Config file           |        644 |
| Kubernetes YAML       |        644 |
| Terraform files       |        644 |
| Jenkins workspace     |        755 |
| Shared project folder |        775 |
| Public web files      |        644 |

---

# 17. Interview Questions

### Q1. What is chmod?

Changes file or directory permissions.

---

### Q2. What is chown?

Changes owner of a file or directory.

---

### Q3. Difference between chmod and chown?

| chmod               | chown             |
| ------------------- | ----------------- |
| Changes permissions | Changes ownership |

---

### Q4. What does 755 mean?

```
Owner

Read
Write
Execute

Group

Read
Execute

Others

Read
Execute
```

---

### Q5. Why is Execute required on directories?

Without Execute permission you cannot enter (`cd`) or traverse the directory.

---

### Q6. Why should we avoid 777?

Everyone can modify the file, creating serious security risks.

---

### Q7. Where is 600 used?

* SSH private keys
* Secrets
* Password files
* Certificates

---

# 18. Common Mistakes

* Using `777` everywhere.
* Confusing `chmod` with `chown`.
* Forgetting execute permission for scripts.
* Giving write permission to everyone.
* Storing private keys with `644` instead of `600`.

---

# 19. Command Cheat Sheet

```bash
ls -l                 # View permissions

chmod 755 file        # Executable

chmod 644 file        # Normal file

chmod 600 file        # Private file

chmod 700 dir         # Private directory

chmod +x script.sh    # Add execute permission

chmod -R 755 project  # Recursive permission change

sudo chown user file

sudo chown user:group file

sudo chgrp group file

umask
```

---

# 20. Key Takeaways

* Linux permissions are based on **Owner**, **Group**, and **Others**.
* Permissions use **Read (4)**, **Write (2)**, and **Execute (1)**.
* Common values:

  * `644` → Regular files
  * `755` → Executable files/directories
  * `600` → Sensitive files
  * `700` → Private directories
* `chmod` changes permissions.
* `chown` changes ownership.
* `umask` controls default permissions.
* Avoid using `777` in production.
* Mastering Linux permissions is essential for working with Docker, Kubernetes, Jenkins, Terraform, and cloud servers.
