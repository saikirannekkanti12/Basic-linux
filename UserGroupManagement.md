# Linux User & Group Management with Real-Time DevOps Examples

## Learning Objectives

After completing this chapter, you should be able to:

* Understand why Linux uses users and groups.
* Explain how Linux authenticates users.
* Create, modify, and delete users and groups.
* Manage file ownership and permissions.
* Configure a shared project directory using the Principle of Least Privilege.
* Understand how enterprise Linux servers manage access.

---

# 1. Why Does Linux Have Users?

Imagine a Linux server without users.

```
Linux Server

├── Application Source Code
├── Database
├── Customer Data
├── SSH Keys
├── Configuration Files
└── Logs
```

If everyone had unrestricted access:

* Anyone could delete production data.
* Anyone could modify application code.
* Anyone could read confidential files.
* Malware would have unrestricted access.

Linux prevents this by assigning an **identity** to every person and service.

```
Linux Kernel

            │

      User Identity

            │

 Permission Check

            │

 Allow / Deny
```

Every file operation goes through the Linux kernel, which decides whether to allow it.

---

# 2. What Is a Linux User?

A Linux user is simply an **identity**.

It can represent:

* A human
* An application
* A background service

Example:

| User    | Purpose              |
| ------- | -------------------- |
| root    | System administrator |
| john    | Developer            |
| alice   | QA Engineer          |
| jenkins | Jenkins service      |
| nginx   | Web server           |
| mysql   | Database server      |

Applications also run as users to limit their privileges.

---

# 3. Why Applications Have Separate Users

Suppose MySQL runs as `root`.

If an attacker compromises MySQL:

```
MySQL

↓

Runs as root

↓

Attacker gains complete server control
```

Instead:

```
MySQL

↓

Runs as mysql user

↓

Can access only

/var/lib/mysql
```

This is called the **Principle of Least Privilege**.

Every service receives only the permissions it actually needs.

---

# 4. Real-Time Company Example

Company:

```
ABC Technologies
```

Developers:

```
John
Alice
Bob
Charlie
```

Project:

```
Inventory Application
```

Linux Server:

```
Ubuntu EC2

/project/inventory-app
```

Each developer has an individual Linux account.

```
john

alice

bob

charlie
```

No one shares credentials.

---

# 5. Authentication Flow

Each developer generates an SSH key pair.

```
Laptop

Private Key
Public Key
```

The administrator stores only the public key.

```
Server

/home/john/.ssh/authorized_keys
```

Login process:

```
Developer Laptop

↓

SSH

↓

Linux Server

↓

Kernel verifies public key

↓

Starts session

↓

User Shell
```

Every developer logs in with their own identity.

---

# 6. Why Not Share One SSH Key?

Wrong approach:

```
company.pem

↓

Shared with everyone
```

Problems:

* No accountability
* Difficult to revoke access
* Everyone logs in as the same user
* Security risk

Correct approach:

```
John

john

Own SSH key

Alice

alice

Own SSH key

Bob

bob

Own SSH key
```

Each user has a unique account and SSH key pair.

---

# 7. Creating Users

Create a new user:

```bash
sudo adduser john
```

Verify:

```bash
id john
```

Example output:

```
uid=1001(john)
gid=1001(john)
```

---

# 8. Modifying Users

Rename a user:

```bash
sudo usermod -l john_dev john
```

Lock an account:

```bash
sudo usermod -L john
```

Unlock:

```bash
sudo usermod -U john
```

Change shell:

```bash
sudo usermod -s /bin/bash john
```

---

# 9. Deleting Users

Delete account only:

```bash
sudo userdel john
```

Delete account and home directory:

```bash
sudo userdel -r john
```

---

# 10. Creating Groups

Create a developers group:

```bash
sudo groupadd developers
```

View groups:

```bash
cat /etc/group
```

---

# 11. Adding Users to a Group

```bash
sudo usermod -aG developers john
sudo usermod -aG developers alice
sudo usermod -aG developers bob
```

Verify:

```bash
groups john
```

Output:

```
john developers
```

---

# 12. Removing Users from a Group

```bash
sudo gpasswd -d john developers
```

---

# 13. Viewing Users

List all users:

```bash
cat /etc/passwd
```

List usernames only:

```bash
cut -d: -f1 /etc/passwd
```

Current user:

```bash
whoami
```

Current user details:

```bash
id
```

---

# 14. Viewing Groups

All groups:

```bash
cat /etc/group
```

Current user's groups:

```bash
groups
```

Specific user's groups:

```bash
groups john
```

---

# 15. Shared Project Scenario

Company project:

```
/project/inventory-app
```

Create directory:

```bash
sudo mkdir -p /project/inventory-app
```

Assign group:

```bash
sudo chgrp developers /project/inventory-app
```

Set permissions:

```bash
sudo chmod 2775 /project/inventory-app
```

Result:

```
drwxrwsr-x
```

Explanation:

* Owner: Read, Write, Execute
* Group: Read, Write, Execute
* Others: Read and Execute
* SGID ensures new files inherit the `developers` group

---

# 16. Why Use SGID?

Without SGID:

John creates:

```
app.py
```

Owner:

```
john
```

Group:

```
john
```

Alice cannot edit the file.

With SGID enabled:

Owner:

```
john
```

Group:

```
developers
```

Alice, Bob, and Charlie can collaborate immediately.

---

# 17. File Ownership

View ownership:

```bash
ls -l
```

Example:

```
-rw-rw-r-- 1 john developers app.py
```

Change owner:

```bash
sudo chown alice app.py
```

Change owner and group:

```bash
sudo chown alice:developers app.py
```

Change group only:

```bash
sudo chgrp developers app.py
```

---

# 18. Principle of Least Privilege

Every user receives only the permissions required to perform their work.

Example:

| User    | Access                              |
| ------- | ----------------------------------- |
| John    | Read/Write `/project/inventory-app` |
| Alice   | Read/Write `/project/inventory-app` |
| Bob     | Read/Write `/project/inventory-app` |
| QA Team | Read-only application logs          |
| MySQL   | `/var/lib/mysql` only               |
| Nginx   | Web content only                    |

Nobody except administrators can modify system files.

---

# 19. Real Enterprise Folder Structure

```
/

├── home

│   ├── john

│   ├── alice

│   ├── bob

├── project

│   └── inventory-app

├── logs

├── backups

├── scripts

└── shared
```

Home directories are private.

Project directories are shared using groups.

---

# 20. Enterprise Access Workflow

```
Developer Laptop

↓

SSH

↓

Linux Server

↓

Authenticate User

↓

Kernel identifies UID and Groups

↓

Permission Check

↓

Access Granted or Denied

↓

Read or Modify Files
```

The Linux kernel enforces all permission checks.

---

# 21. Offboarding an Employee

Suppose Bob leaves the company.

Steps:

1. Lock the account:

```bash
sudo usermod -L bob
```

2. Remove SSH public key:

```
/home/bob/.ssh/authorized_keys
```

3. Remove group membership:

```bash
sudo gpasswd -d bob developers
```

4. Delete account (if required):

```bash
sudo userdel -r bob
```

Other users continue working without interruption.

---

# 22. DevOps Best Practices

* Give every employee an individual Linux account.
* Never share SSH private keys.
* Authenticate with SSH public keys.
* Manage access using groups rather than individual permissions.
* Use SGID on shared project directories.
* Apply the Principle of Least Privilege.
* Remove or lock user accounts immediately when employees leave.
* Avoid granting root access; use `sudo` only when administrative tasks are required.

---

# 23. Interview Questions

### Why do Linux services such as Jenkins, MySQL, and Nginx run as separate users?

To isolate each service so that a compromise affects only that service's files and resources, reducing the impact of an attack.

---

### Why should every developer have a separate Linux account?

For security, accountability, auditing, and easy onboarding/offboarding.

---

### Why should SSH private keys never be shared?

A private key uniquely identifies a user. Sharing it removes accountability and makes it impossible to revoke access for one individual without affecting everyone else.

---

### Why use groups instead of assigning permissions to each user?

Groups simplify administration. Adding or removing users from a group automatically changes their access without modifying file permissions individually.

---

### What is the Principle of Least Privilege?

Grant users and services only the minimum permissions necessary to perform their tasks. This reduces the risk of accidental changes and limits the impact of security breaches.

---

# Summary

* Linux uses **users** to represent people and services.
* **Groups** simplify permission management for teams.
* The **Linux kernel** checks permissions for every file operation.
* Every employee should have a **unique Linux account** and **individual SSH key pair**.
* Shared project directories should use **groups** and **SGID** for collaboration.
* The **Principle of Least Privilege** is a core security practice in Linux administration and DevOps.
* These concepts are fundamental for managing servers on AWS, Kubernetes nodes, CI/CD systems, and enterprise Linux environments.
