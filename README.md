## About `doctl`

### What Is `doctl`?
`doctl` is the official DigitalOcean command-line interface (CLI).

> [!note]
> CLI is a text-based interface that allows users to interact with the operating system. 

[^1]
### What Does It Do?
It allows users to interact with the DigitalOcean API via the command line interface. Users can create, configure, and destroy DigitalOcean resources like Droplets, Kubernetes clusters, firewalls, load balancers, database clusters, domains, and more.[^11]

### Why Use `doctl`?
`doctl` allows experienced users to manage resources faster than using the web interface. With `doctl`, system administrators can automate repetitive tasks, which is helpful for scaling infrastructure. Moreover, since `doctl` is a command-line interface, it reduces the resources that hardware will use, making it less resource-intensive.[^7]

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
    - `wget` is the package name used to download files from the web.
- `cd` changes the directory.
    - `~` represents the home directory.
- `tar` (tape archive) creates archive files and extracts archive files.
- `mv` moves files and directories to another directory or renames them.

### Step 2: Create an API Token
An API token is a string of codes containing data that identifies a specific user.

To use DigitalOcean `doctl`, the API token identifies and confirms your identity to the DigitalOcean API (authentication).

The API defines the permissions for you to perform certain actions, such as creating, deleting, or accessing Droplets.[^16]

To create an API token for DigitalOcean:: 
1. Go to [DigitalOcean | Cloud Infrastructure for Developers](https://www.digitalocean.com/) and log in or sign up.
2. On the home page, click **API**.


![alt text](./asset/Pasted%20image%2020240917185310.png)


3. Click **Generate Token**.
   
![alt text](./asset/Pasted%20image%2020240917190812.png)<br>
<br>
Personal Acess token information<br>
    - Token Name: Choose the name of the token.<br>
    - Expiration: Choose when the token expires; it will make the token unable to authenticate to the API after the interval passes.<br>
    - Scope: Choose the permissions that the token will have:<br>
        - Custom Scope: Lets you specify the scope from the full list of scopes.<br>
        - Read Scope: Grants the permission to read all resources.<br>
        - Full Scope: Grants all permissions.<br>
1. Copy the token (warning! The token will only be shown once).
![alt text](./asset/Pasted%20image%2020240917192752.png)

### Step 3: Authenticate `doctl` by Using the API

in order to connect the token to doctl, you need to authenticate the API with doctl by doing the following step <br>

1. To initialize authentication and give the authentication a name:
    ```bash
    doctl auth init --context token1
    ```

**Command :**
- `doctl auth init`: Authenticates the account with the token.

**Command Argument:**
- `--context`: Takes the argument of the token name.

2. Run the command and then paste the token.

Multiple API <br>
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
![alt text](./asset/Pasted%20image%2020240917195939.png)

## SSH Key

When you create an SSH key, your computer makes a pair of keys: a public key and a private key. The public key can be shared and is used to encrypt data, while the private key is kept secure on your computer and is used to decrypt data. This key pair is used to securely connect to remote servers over the SSH protocol, without needing to use passwords.[^13]

### To Create an SSH Key in Arch Linux
1. To create an SSH key, use the command:
    ```bash
    ssh-keygen -t ed25519 -f ~/.ssh/doctlkey -C "comment"
    ```
2. Type the passphrase (password) for this key (it can be left empty).

**Command Explanation:**
- `ssh-keygen`: Creates public and private keys.<br>
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
- `doctl compute ssh-key import`: Adds the SSH key to the DigitalOcean account.<br>
**Command Argument:**
- `--public-key-file`: Path to the public key.

2. Check if the SSH key is imported to DigitalOcean:
    ```bash
    doctl compute ssh-key list
    ```
The command will print the list of SSH keys in DigitalOcean.


## Preparation for Create Droplet with Cloud init

requirements 
1. image
2. configfile for clound init (.ymal)
3. droplet 

### What is image
Images is similar to a blueprint and contain all the necessary components to run a Docker container. it can be reused and deployed by any host  [^3]


### To Upload The Custom Image 
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
command `doctl compute create` require `--region, --image-url`[^3]


### Create Config File For Clound Init

#### what is cloud init
The cloud-init tool automates the setup of cloud instances when the system starts. It can be used to set the hostname, install packages, and run scripts on the instance.




#### what is .ymal
.ymal file is human readable text, often used as config file. In the following step, we use the Ymal file to create and set the config for cloud-inti to create a droplet. [^17]


1. on **CLI** create file .yaml (can be anywhere)
```bash
nvim droplet_setting.ymal
```
1. the content of droplet_setting.ymal 
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
1. save and quit `:wq` 

**explanation of .ymal file** [^17]<br>
user : store the user information for the new droplet that you are going to create
- name : the user's login name 
- group : optional, Additional groups to add the user to
- shell : is the default shell path for user
- sudo : accept a sudo rules string( in this example is everyone can use without password)
- ssh-authorized-keys: a public key, for user to be able to connect to the droplet with private key <br>
Packages: package is an archive with software file, configuration files and dependencies
in the example it downloads the following packages
  - ripgrep, search tool
  - rsync, file copy tool which can copy from to/from remote host
  - neovim, file editor tool that is a fork from vim (better performance)
  - fd, find the entries in file system
  - less, is a linux terminal pager that shows a file's contents one screen at a time
  - man-db,  is used to create or update the manual page index caches on Linux.
  - bash-completion is a package that contain a collection of command line command completions for the shell
  - tmux is a package that allows you to create, manage, and switch between multiple terminal sessions within a single window,
overall this package are commonly used tool in Linux environment.

disable_root: Prevent unauthorized access by restricting the ability to log in or perform operations as the root user.[^10]


### Create Arch Linux Droplet with Clound-init (require .ymal)[^4][^8]
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
- `--image` takes an argument of image ID or image slug
- `--size` takes arguments of the size of the VCPU and ram in the form of `s-[number]vcpu-[number]gb`
- `--region` takes an argument of the region where you want to create a droplet. It should be close to the end user
- `--user-data-file` takes the argument of the path to a config file (.ymal)
- `--wait` takes an argument of the name of the droplet. You can add multiple arguments to create multiple droplets. 
	ex `--wait assignment1 assignment2 ` [^6]


### SSH to New Droplet
this is a process where you connect to your new droplet by using the command `ssh` to connect to the new droplet by private key.

However, before connecting to the Droplet, you need to create a configuration file to specify the SSH protocol settings and connection details. This configuration file simplifies the connection process by storing all necessary options, such as the IP address, user name, and path to the private key." [^18]
1. in the directory of `/ssh` create the file name `config`
``` bash
nvim config
```
2. in the config file, set the SSH connection 
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

commands
- `nvim` file editor, to create a file in config
- `ssh`, login to the remote machine

in the config file
- host is a name that the user use to connect to the new droplet (ssh nameOftheDroplet)
- Hostname is the IP address of the droplet you trying to connect
- PreferredAuthentications is a authentications method, in this example we use a public key
- IdentifyFile is the path of public key
- StrictHostKeyChecking is Automatically accepts new or changed host keys without prompting, making SSH connections easier but less secure.
- UserKnownHostsFile /dev/null: Disables saving and checking host keys by directing SSH to use a file that discards all data, effectively turning off host key verification.



After you connect to the new droplet, you will see on the terminal that you have sucessfully connect to the new droplet 

![alttext](./asset/Pasted%20image%2020240927212638.png)




## Extra Commands I find helpful during this assignment 
- `doctl compute image list` show the list of images 
- `doctl compute droplet list` show the list of droplet 
- `doctl compute droplet delete <dropletID>` delete droplet 
- `doctl compute image list <imageID>` delete image 
- `doctl compute ssh-key list` show the list of SSH keys
`



## Source


[^1]:SolarWinds. (n.d.). Using journalctl: The ultimate guide to logging. Loggly. Retrieved from https://www.loggly.com/ultimate-guide/using-journalctl/

[^2]:DigitalOcean. (n.d.). How to destroy a droplet from the DigitalOcean control panel. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/droplets/how-to/destroy/

[^3]:DigitalOcean. (n.d.). How to upload custom images. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/custom-images/how-to/upload/

[^4]:DigitalOcean. (n.d.). How to create droplets from custom images. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/custom-images/how-to/create-droplets/

[^5]:DigitalOcean. (n.d.). doctl compute image. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/reference/doctl/reference/compute/image/

[^6]:DigitalOcean. (n.d.). doctl compute droplet. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/

[^7]:DigitalOcean. (2024, June 21). _doctl command line interface (CLI)_. DigitalOcean Documentation. Retrieved September 27, 2024, from [https://docs.digitalocean.com/reference/doctl/](https://docs.digitalocean.com/reference/doctl/)

[^8]:DigitalOcean. (n.d.). doctl compute droplet create. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/create/


[^9]:DigitalOcean. (n.d.). How to automate droplet setup with cloud-init. DigitalOcean Documentation. Retrieved from https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/

[^10]:Arch Linux. (n.d.). Pacman. ArchWiki. Retrieved from https://wiki.archlinux.org/title/Pacman

[^11]:Cloud-init. (n.d.). Cloud config examples. Cloud-init Documentation. Retrieved from https://cloudinit.readthedocs.io/en/latest/reference/examples.html


[^12]:Cyberciti. (n.d.). Mandb command. Linux Bash Shell Scripting Tutorial Wiki. Retrieved from https://bash.cyberciti.biz/guide/Mandb_command

[^13]:SSH Communications Security. (n.d.). _SSH key management: Best practices and pitfalls_. SSH Academy. Retrieved September 27, 2024, from [https://www.ssh.com/academy/ssh-keys](https://www.ssh.com/academy/ssh-keys)

[^14]:[Containers and Images | DigitalOcean Documentation](https://docs.digitalocean.com/products/images/)

[^15]: Kerrisk, M. (2024, April 5). _mandb(8) — Linux manual page_. Man7.org. Retrieved September 27, 2024, from [https://man7.org/linux/man-pages/man8/mandb.8.html](https://man7.org/linux/man-pages/man8/mandb.8.html)

[^16]:DigitalOcean. (n.d.). [Create a personal access token](https://docs.digitalocean.com/reference/api/create-personal-access-token/). DigitalOcean Documentation. Retrieved September 27, 2024, from [https://docs.digitalocean.com/reference/api/create-personal-access-token/](https://docs.digitalocean.com/reference/api/create-personal-access-token/)

[^17]:Cloud-init. (n.d.). [Cloud config examples](https://cloudinit.readthedocs.io/en/latest/reference/examples.html). Read the Docs. Retrieved September 27, 2024, from [https://cloudinit.readthedocs.io/en/latest/reference/examples.html](https://cloudinit.readthedocs.io/en/latest/reference/examples.html)

[^18]: Linuxize. (2020, February 8). [Using the SSH config file](https://linuxize.com/post/using-the-ssh-config-file/). Linuxize. Retrieved September 27, 2024, from [https://linuxize.com/post/using-the-ssh-config-file/](https://linuxize.com/post/using-the-ssh-config-file/)