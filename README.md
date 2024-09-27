## About `doctl`

### What Is `doctl`?
`doctl` is the official DigitalOcean command-line interface (CLI).

> [!note]
> CLI is a text-based interface that allows users to interact with the operating system.

### What Does It Do?
It allows users to interact with the DigitalOcean API via the command line interface. Users can create, configure, and destroy DigitalOcean resources like Droplets, Kubernetes clusters, firewalls, load balancers, database clusters, domains, and more.

### Why Use `doctl`?
`doctl` allows experienced users to manage resources faster than using the web interface. With `doctl`, system administrators can automate repetitive tasks, which is helpful for scaling infrastructure. Moreover, since `doctl` is a command-line interface, it reduces the resources that hardware will use, making it less resource-intensive.

## How to Install `doctl`
This is a step-by-step guide on how to install `doctl` on Arch Linux.

### Step 1: Download `doctl` with `wget`

1. Download/update the `wget` package:
    ```bash
    sudo pacman -Sy wget
    ```
2. Download `doctl` using `wget`:
    ```bash
    cd ~
    wget https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-linux-amd64.tar.gz
    ```
3. Extract the file (binary):
    ```bash
    tar xf ~/doctl-1.110.0-linux-amd64.tar.gz
    ```
4. Move the file (binary) to the path (bin):
    ```bash
    sudo mv ~/doctl /usr/local/bin
    ```

**Commands Explanation:**
- `sudo` temporarily elevates privileges.
- `pacman` is the package manager (collection of software tools).
- `sudo pacman -Sy wget`:
    - `S` syncs to install the package.
    - `y` updates the package.
    - `wget` is the package name, used for downloading files from the web.
- `cd` changes the directory.
    - `~` represents the home directory.
- `tar` (tape archive) creates archive files and extracts archive files.
- `mv` moves files and directories to another directory or renames them.

### Step 2: Create an API Token
An API token is a string of codes containing data that identifies a specific user.

To use DigitalOcean `doctl`, the API token identifies and confirms your identity to the DigitalOcean API (authentication).

The API determines the permissions for you to perform certain actions, such as creating, deleting, or accessing Droplets.

1. Go to [DigitalOcean | Cloud Infrastructure for Developers](https://www.digitalocean.com/) and log in or sign up.
2. On the home page, click **API**.
3. Then click **Generate Token**.
4. Type the token name and choose the expiration and scopes:
    - Token Name: Choose the name of the token.
    - Expiration: Choose when the token expires; it will make the token unable to authenticate to the API after the interval passes.
    - Scope: Choose the permissions that the token will have:
        - Custom Scope: Lets you specify the scope from the full list of scopes.
        - Read Scope: Grants the permission to read all resources.
        - Full Scope: Grants all permissions.
5. Copy the token (warning! The token will only be shown once).

### Step 3: Authenticate `doctl` by Using the API

1. To initialize authentication and give the authentication a name:
    ```bash
    doctl auth init --context token1
    ```

**Command Explanation:**
- `doctl auth init`: Authenticates the account with the token.

**Command Argument:**
- `--context`: Takes the argument of the token name.

2. Run the command and then paste the token.

> [!note] Multiple API
You can add multiple accounts and switch between authenticated accounts by using:
```bash
doctl auth switch --context [name]
```
and
```bash
doctl auth list
```
to see the list of authenticated accounts you have.

### To Confirm `doctl` is Working
After running the authentication, run:
```bash
doctl account get
```
The result should look like this: <br>
![Pasted image](Pasted image 20240917195939.png)

## SSH Key

An SSH key is an access credential used in the SSH protocol. It serves a similar function to usernames and passwords, but SSH keys are mainly used for automated processes and implementing single sign-on by system administrators and power users.

When you create an SSH key, your operating system generates a pair of cryptographic keys: a public key and a private key. The public key can be freely shared and is used to encrypt data that can only be decrypted by the corresponding private key, which is kept secure on your local machine. This key pair is essential for establishing secure, password-less connections to remote servers over the SSH protocol.

### To Create an SSH Key in Arch Linux
1. To create an SSH key, use the command:
    ```bash
    ssh-keygen -t ed25519 -f ~/.ssh/doctlkey -C "comment"
    ```
2. Type the passphrase (password) for this key (it can be left empty).

**Command Explanation:**
- `ssh-keygen`: Creates public and private keys.
**Command Arguments:**
- `-t`: Type of encryption.
- `-f`: Filename (directory).
- `-C`: Comment.

### Upload Public Keys to `doctl`
To create a Droplet using automated configuration (e.g., cloud-init), it requires the SSH public key to be accessible to the Droplet at the application.

1. By running the command:
    ```bash
    doctl compute ssh-key import git-user --public-key-file /Users/example-user/.ssh/git-user.pub
    ```

**Command Template:**
```bash
doctl compute ssh-key import [key-name] --public-key-file [path]
```
**Command Explanation:**
- `doctl compute ssh-key import`: Adds the SSH key to the DigitalOcean account.
**Command Argument:**
- `--public-key-file`: Path to the public key.

2. Check if the SSH key is imported to DigitalOcean:
    ```bash
    doctl compute ssh-key list
    ```
The command will print the list of SSH keys in DigitalOcean.


## Create Droplet using `doctl`
---

### What is image
Images serve as a blueprint and contain all the necessary components to run a Docker container. This enables reusability and deployment across different hosts.

### to Upload The Custom Image 
use command 
```

doctl compute image create  arch_linux --image-url https://gitlab.archlinux.org/archlinux/arch-boxes/-/package_files/7527/download --region nyc1
```
command
- `doctl compute image` create image 
command arguments
- `--image-url` URL of the image file.
- `--region` is a flag used to specify the region.
- `--description` is a flag used to provide a description of the image.
command `doctl compute create` require `--region, --image-url`


### Create Config File For Clound Init
1. on **CLI** create file .yaml (can be anywhere)
```bash
nvim droplet_setting.ymal
```
2. the content of droplet_setting.ymal 
```ymal
#cloud-config
users:
  - name: user-name #change me
    primary_group: group-name #change me
    groups: wheel
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-ed25519 ...
packages:
  - ripgrep
  - rsync
  - neovim
  - fd
  - less
  - man-db
  - bash-completion
  - tmux
disable_root: true
```
3. save and quit `:wq` 

**explanation of .ymal file** <br>
user : store the user information for the new droplet that you are going to create
- name : the user's login name 
- group : optional, Additional groups to add the user to
- shell : is the default shell path for user
- sudo : accept a sudo rules string( in this example is everyone can use without password)
- ssh-authorized-keys: a public key, for user to be able to connect to the droplet with private key
Packages: package is a archive with software file, configuration files and dependencies
in the example it download the following package
  - ripgrep, search tool
  - rsync, file copy tool which can copy from to/from remote host
  - neovim, file editor tool that is a fork from vim (better performance)
  - fd, find the entries in file system
  - less, is a linux terminal pager that shows a file's contents one screen at a time
  - man-db,  is used to create or update the manual page index caches on Linux.
  - bash-completion provides automatic completion for command-line commands, options, and arguments in the Bash shell, helping to speed up and simplify command entry
  - tmux allows you to create, manage, and switch between multiple terminal sessions within a single window,
overall this package are commonly used tool in Linux environment.They enhance productivity and provide additional functionality.
disable root : disable_root to prevent unauthorized access by restricting the ability to log in or perform operations as the root user.


### Create Arch Linux Droplet with Clound-init (require .ymal)
1. get the image id of archlinux
```bash
doctl compute image list
```
2. get the ssh-key id 
```
doctl compute ssh-key list
```
3. create droplet 
```bash
doctl compute droplet create --image 166289307 --size s-1vcpu-1gb --region sfo3 --ssh-keys 43495109 --user-data-file ~/.ssh/droplet_setting.yaml --wait assignment1
```
command
- `doctl compute droplet create` create droplet
command arguments 
- `--image` take argument of image ID or image slug
- `--size` take arguments of the size of the VCPU and ram in form of `s-[number]vcpu-[number]gb`
- `--region` take argument of the region where you want to create droplet, it should close to the end user
- `--user-data-file` take the argument of the path to config file (.ymal)
- `--wait` take argument of the name of droplet. You can add multiple argument to create multiple droplet. 
	ex `--wait assignment1 assignment2 `


### SSH to New Droplet
1. in the directory of `/ssh` create file name `config`
``` bash
nvim config
```
2. in the config file set the SSH connection 
```txt
Host assigment1 #host name, can be anything
  HostName 143.110.232.242 # ip address of droplet you are trying to connect
  User arch # user
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/doctl_key #path to your ssh-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```
3. connect to your new droplet
```bash
ssh assigment1
```









## Extra Commands I find useful during this assignment 
- `doctl compute image list` show the list of images 
- `doctl compute droplet list` show the list of droplet 
- `doctl compute droplet delete <dropletID>` delete droplet 
- `doctl compute image list <imageID>` delete image 
- `doctl compute ssh-key list` show the list of SSH keys
`



## Source


SolarWinds. (n.d.). Using journalctl: The ultimate guide to logging. Loggly. Retrieved from https://www.loggly.com/ultimate-guide/using-journalctl/

DigitalOcean. (n.d.). How to destroy a droplet from the DigitalOcean control panel. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/droplets/how-to/destroy/

DigitalOcean. (n.d.). How to upload custom images. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/custom-images/how-to/upload/

DigitalOcean. (n.d.). How to create droplets from custom images. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/custom-images/how-to/create-droplets/

DigitalOcean. (n.d.). doctl compute image. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/reference/doctl/reference/compute/image/

DigitalOcean. (n.d.). doctl compute droplet. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/

DigitalOcean. (n.d.). doctl compute droplet create. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/create/

Snapcraft. (n.d.). Installing snap on Arch Linux. Snapcraft Documentation. Retrieved from https://snapcraft.io/docs/installing-snap-on-arch-linux

DigitalOcean. (n.d.). How to automate droplet setup with cloud-init. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/

Arch Linux. (n.d.). Pacman. ArchWiki. Retrieved from https://wiki.archlinux.org/title/Pacman

Cloud-init. (n.d.). Cloud config examples. Cloud-init Documentation. Retrieved from https://cloudinit.readthedocs.io/en/latest/reference/examples.html

GitLab. (n.d.). 2420-notes/week-three.md. Retrieved from https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-three.md

Cyberciti. (n.d.). Mandb command. Linux Bash Shell Scripting Tutorial Wiki. Retrieved from https://bash.cyberciti.biz/guide/Mandb_command