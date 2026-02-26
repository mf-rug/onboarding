## Connecting to the command line

You need to open a terminal (also called the command line). This is a text-based interface where you type commands instead of clicking buttons.

- If you are on macOS or Linux, you can simply open the Terminal application (search for “Terminal” in your search bar).
- If you are on Windows, you need a terminal-like program. A commonly used option is MobaXterm, which works similarly to a macOS/Linux terminal and supports SSH connections to clusters.

Once your terminal is open, you can connect to the cluster using the login instructions provided [here](https://wiki.hpc.rug.nl/habrok/connecting_to_the_system/connecting). 

---

## Basic commands for navigating the command line

These are the most important commands you will use when working on the cluster:

- `ls`  
  Lists the contents (files and folders) of your current location

- `pwd`  
  Shows the full path of your current location

- `cd [path]`  
  Moves you to a new directory (folder)  
  Example: `cd /scratch/pxxxxxx`

- `cp [file] [path]`  
  Copies a file to a new location or name. Add `-r` if you are copying directories (folders)

- `rm [file]`  
  Removes (deletes) a file. Add `-r` to remove directories  
  Be careful: deleted files cannot be easily recovered.

- `mkdir [path]`  
  Creates a new directory (folder) at the specified path or with the given name  

---

## Other useful commands

- `man [command]`  
  Opens the manual page of a command (press `q` to quit the manual)

- `nano [file]`  
  Opens a simple text editor in the terminal  
  Use `Ctrl + O` to save, then `Ctrl + X` to exit

- `realpath [file]`  
  Shows the full absolute path of a file  

---

## Transferring files to and from the cluster

To move files between your computer and the cluster, you can use tools such as `scp` or `rsync`. `rsync` is recommended because it is reliable and works well for large files and folders. The following commands must be run in your **local terminal** (not while logged into the cluster).

### Upload a file to the cluster

This copies a file from your computer to the cluster.

```bash
rsync path/to/file [p-number]@[login-node]:/path/to/destination
```

Example:
```bash
rsync structure.pdb pxxxxxx@login1.hb.hpc.rug.nl:/scratch/pxxxxxx/boltz/
```

Download a file from the cluster
```bash
rsync [p-number]@[login-node]:/path/to/file /path/to/destination
```
Examples:

```bash
rsync pxxxxxx@login1.hb.hpc.rug.nl:/scratch/pxxxxxx/structure.pdb .
rsync -r pxxxxxx@login1.hb.hpc.rug.nl:/scratch/pxxxxxx/Boltz/results .
```
- The -r flag is used when copying directories (folders)
- . means “download to the current folder” on your computer
