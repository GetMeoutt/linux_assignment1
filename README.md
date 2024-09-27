




## About `doctl`

### What Is `doctl`? 
`doctl` is the official Digital Ocean command-line (CLI)

>[!note]
> CLI is text-base interface that allow user to interact with operate system


### What Does it do?
it allows user to interact with the Digital Ocean API via command line interface. User can create, configure, and destroy Digital Ocean resources like Droplets, Kubernetes clusters, firewalls, load balancers, database clusters, domains, and more.



### Why Use `doctl`?
 ``doctl``  allows experienced user to manage faster than go through the web interface. With `doctl`, system administrators can automate competitive task, which is helpful for scaling infrastructure. Moreover, `doctl` is command line interface, it reduce the resource that hardware will use, making it less resource intensive. 



## How to install `doctl` 
this is step by step of how to install `doctl` on Arch Linux
### Step 1 Download `doctl` with `wget`

1. download / update `wget` package 
```bash 
sudo pacman -Sy wget
```
2.  download `doctl` using `wget`
```bash
cd ~
wget https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-linux-amd64.tar.gz
```
3. extract the file (binary) 
```bash
tar xf ~/doctl-1.110.0-linux-amd64.tar.gz
```
4. move file(binary) to the path (bin)
```bash
sudo mv ~/doctl /usr/local/bin
```

commands
- `sudo` temporarily elevates privileges 
- `pacman` is package manager (collection of software tool)
- `sudo pacman -Sy wget`
	- `S` sync to install package
	- `y` to update package 
	- `wget` is package name, and it used from downloading file from the web
- `cd` change directory
	- '~' home directory 
- `tar` (tape archive) create archive file and extract archive file
- `mv` move, is to move files and directories to other directory or rename it 



### Step 2: Create an API token 
API token is a string of codes containing a comprehensive data that identifies a specifies user. 

In order to use Digital ocean `doctl`, the API token is identified and confirms your identity to Digital Ocean API (authentication).

API determine the permission for you to perform certain action. For example, Create, Delete , access Droplet.

1.  go to [DigitalOcean | Cloud Infrastructure for Developers](https://www.digitalocean.com/) then login or signup 

2. on the home page click **API** 
![[Pasted image 20240917185310.png]]

4. then click **generate token** 
![[Pasted image 20240917185825.png]]

6. type the token name and choose the expiration and scopes
	 - token name :  choose the name of the token
	- Expiration : choose when the token expires, it will make the token unable to authenticate to API after the interval passes
	- Scope : choose the permission that allow token to do
		- custom scope : let you specific the scope from the full list of scope
		- read scope :  grant the permission to read for all resource
		- full scope : grant all permission
	
![[Pasted image 20240917190812.png|450]]

4. copy the the token (warning! token only show once)
![[Pasted image 20240917192752.png]]

### Step 3 : Authenticate `doctl` by using  API 

1. to Initializes authentication and give the authentication name
``` bash
doctl auth init --context token1
```
command
- `doctl auth init` authenticate the account with token

command argument
- `--context` take argument of token name 

2. run the command then, paste the token

> [!note]   multiple API
you can add multiple account and switch authentication account by using 
doctl auth switch --context [name] and doctl auth list to see the list of authentication you have


### to Confirm `doctl` is working
After run the authentication, run `doctl account get`. The result should look like this: <br>
![[Pasted image 20240917195939.png]]



## SSH-key 

what is `SSH-Key`?





### How to create SSH-KEY in Linux 
 1. to create a SSH key use command
```bash
`ssh-keygen -t ed25519 -f ~/.ssh/doctlkey -C "commet"
```
2. type the passphrase (password )for this  key (can be leave it empty)

 command 
 - `ssh-keygen` create public and private key 
 command argument 
- -t type for encryption
- -f filename (directory)
- -C comment

### Upload Public Keys to `doctl`
to create a droplet using automate configuration (ex .clound-init), it require SSH public key to be accessible to the droplet at the application. 

1. by running the command, 
```bash
doctl compute ssh-key import git-user --public-key-file /Users/example-user/.ssh/git-user.pub

```
command template 
`doctl compute ssh-key import [key-name] --public-key-file [path]`
command
- `doctl compute ssh-key import `  add ssh-key to Digital Ocean account
command argument 
- `--public-key-file` : path to public 

2. check if the ssh-key is imported to Digital Ocean 
```bash 
doctl compute ssh-key list
```
the command will print the list of ssh-key in Digital Ocean


## Create Droplet using `doctl`

### To Upload The Custom Image 
what is image



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
  - man-db, 
  - bash-completion
  - tmux



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
Host as1 #host name, can be anything
  HostName 143.110.232.242 # ip address of droplet you are trying to connect
  User arch # user
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/doctl_key #path to your ssh-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```
3. connect to your new droplet
```bash
ssh as1
```









## Extra Commands I find useful during this assignment 
- `doctl compute image list` show the list of images 
- `doctl compute droplet list` show the list of droplet 
- `doctl compute droplet delete <dropletID>` delete droplet 
- `doctl compute image list <imageID>` delete image 
- `doctl compute ssh-key list` show the list of SSH keys
`



## Source


SolarWinds. (n.d.). _Using journalctl: The ultimate guide to logging_. Loggly. [https://www.loggly.com/ultimate-guide/using-journalctl/](https://www.loggly.com/ultimate-guide/using-journalctl/)

[How to Destroy a Droplet from the DigitalOcean Control Panel | DigitalOcean Documentation](https://docs.digitalocean.com/products/droplets/how-to/destroy/)

[How to Upload Custom Images | DigitalOcean Documentation](https://docs.digitalocean.com/products/custom-images/how-to/upload/)

[How to Create Droplets from Custom Images | DigitalOcean Documentation](https://docs.digitalocean.com/products/custom-images/how-to/create-droplets/)

[doctl compute image | DigitalOcean Documentation](https://docs.digitalocean.com/reference/doctl/reference/compute/image/)

[doctl compute droplet | DigitalOcean Documentation](https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/)

[doctl compute droplet create | DigitalOcean Documentation](https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/create/)

[Installing snap on Arch Linux | Snapcraft documentation](https://snapcraft.io/docs/installing-snap-on-arch-linux)

[How to Automate Droplet Setup with cloud-init | DigitalOcean Documentation](https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/)

[pacman - ArchWiki (archlinux.org)](https://wiki.archlinux.org/title/Pacman)

[Cloud config examples - cloud-init 24.3.1 documentation (cloudinit.readthedocs.io)](https://cloudinit.readthedocs.io/en/latest/reference/examples.html)

[2420-notes/week-three.md · main · cit_2420 / 2420-notes-F24 · GitLab](https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-three.md)
