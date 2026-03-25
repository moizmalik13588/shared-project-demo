# Linux Multi-User Shared Project Setup

## Overview

This repository demonstrates a **multi-user shared folder setup** on Linux (Ubuntu/WSL), covering user management, group permissions, and workflow testing.

| Detail | Value |
|--------|-------|
| **Users** | `devuser`, `tester` |
| **Group** | `devproject` |
| **Shared Folder** | `/home/shared_project` |
| **Permissions** | `2775` + `umask 002` |

All files and folders created inside `shared_project` automatically inherit the `devproject` group.

---

## Users & Groups Setup

```bash
# Add users
sudo adduser devuser
sudo adduser tester

# Create shared group
sudo groupadd devproject

# Add both users to group
sudo usermod -aG devproject devuser
sudo usermod -aG devproject tester
```

Verify group membership:

```bash
id devuser
id tester
```

---

## Shared Folder Setup

```bash
# Create shared folder
sudo mkdir /home/shared_project

# Assign group ownership
sudo chown :devproject /home/shared_project

# Set permissions (2775 = setgid + rwxrwxr-x)
sudo chmod 2775 /home/shared_project
```

**Why `2775`?**
- `2` = setgid bit — new files/folders inherit `devproject` group automatically
- `7` = owner has full read/write/execute
- `7` = group has full read/write/execute
- `5` = others can read and execute only

**Why `umask 002`?**
- Ensures new files are group-writable by default
- Add to each user's `~/.bashrc`: `umask 002`

---

## Multi-User Workflow Testing

### As `devuser`

```bash
su - devuser
cd /home/shared_project
touch devuser_file.txt
mkdir devuser_folder
ls -l
```

### As `tester`

```bash
su - tester
cd /home/shared_project
touch tester_file1.txt
mkdir tester_folder
ls -l
```

All created files and folders will show group `devproject` with correct permissions.

---

## Verification Commands

```bash
# Check file permissions and group ownership
ls -l /home/shared_project

# Verify group inheritance on new file
touch /home/shared_project/test_file.txt
ls -l /home/shared_project/test_file.txt

# Check user IDs and group memberships
id devuser
id tester
```

---

## Scripts

The `scripts/` folder contains example scripts demonstrating permissions and workflow:

```
scripts/
└── example_script.sh
```

---

## Key Concepts

- **setgid bit (`2`)** — group ownership is inherited by all new files/folders
- **`umask 002`** — group write permission enabled for all new files
- **`authorized_keys`** — for SSH key-based access per user (if remote access needed)
