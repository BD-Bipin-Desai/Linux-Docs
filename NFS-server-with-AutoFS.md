## NFS Server Setup (Linux) – Steps with Commands

### 1. Install NFS Packages

#### On NFS Server

For RHEL/CentOS:

```bash
sudo yum install nfs-utils -y
```

For Ubuntu/Debian:

```bash
sudo apt install nfs-kernel-server -y
```

---

### 2. Create Shared Directory

```bash
sudo mkdir -p /nfs_share
sudo chmod 777 /nfs_share
```

Add test file:

```bash
echo "NFS Shared File" | sudo tee /nfs_share/test.txt
```

---

### 3. Configure NFS Exports

Edit exports file:

```bash
sudo vi /etc/exports
```

Add:

```bash
/nfs_share 192.168.1.0/24(rw,sync,no_root_squash)
```

Apply configuration:

```bash
sudo exportfs -rav
```

---

### 4. Start and Enable NFS Services

#### RHEL/CentOS

```bash
sudo systemctl enable --now nfs-server
sudo systemctl status nfs-server
```

#### Ubuntu

```bash
sudo systemctl enable --now nfs-kernel-server
sudo systemctl status nfs-kernel-server
```

---

### 5. Configure Firewall

#### RHEL/CentOS

```bash
sudo firewall-cmd --permanent --add-service=nfs
sudo firewall-cmd --reload
```

---

## Client Configuration

### 6. Install NFS Client Packages

#### RHEL/CentOS

```bash
sudo yum install nfs-utils autofs -y
```

#### Ubuntu/Debian

```bash
sudo apt install nfs-common autofs -y
```

---

### 7. Create Mount Directory

```bash
sudo mkdir -p /mnt/nfs_share
```

---

### 8. Mount NFS Share Manually

```bash
sudo mount <NFS_SERVER_IP>:/nfs_share /mnt/nfs_share
```

Verify:

```bash
df -h
```

---

# AutoFS Configuration (Automatic Mounting)

### 9. Configure AutoFS Master File

Edit:

```bash
sudo vi /etc/auto.master
```

Add:

```bash
/mnt /etc/auto.nfs
```

---

### 10. Create AutoFS Map File

```bash
sudo vi /etc/auto.nfs
```

Add:

```bash
nfs_share -rw,sync <NFS_SERVER_IP>:/nfs_share
```

---

### 11. Restart AutoFS Service

```bash
sudo systemctl restart autofs
sudo systemctl enable autofs
```

Test:

```bash
cd /mnt/nfs_share
ls
```

---

# Apply ACL for Secure Access Control

### 12. Install ACL Package

```bash
sudo yum install acl -y
```

or

```bash
sudo apt install acl -y
```

---

### 13. Apply ACL Permissions

Give user `developer` read/write access:

```bash
sudo setfacl -m u:developer:rwx /nfs_share
```

Verify ACL:

```bash
getfacl /nfs_share
```

---

# Verification Commands

Check exports:

```bash
showmount -e <NFS_SERVER_IP>
```

Check mounted shares:

```bash
mount | grep nfs
```

Check AutoFS:

```bash
systemctl status autofs
```
