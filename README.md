

### `doctl`
---
#### what is doctl? 
`doctl` is the official Digital Ocean command-line (CLI)

[note] CLI is text-base interface that allow user to interact with operate system
##### what Does it do?
it allow user to interact with the Digital Ocean *API* via command line. User can create, configure, and destroy DigitalOcean resources like Droplets, Kubernetes clusters, firewalls, load balancers, database clusters, domains, and more.

##### why use `doctl`?
`about CLI`


#### How to install `doctl` 
---
this is a step by step of how to install doctl via CMD on the Window OS 

##### Step 1 download `doctl` 
0. open **terminal** 
1. run the following command to download the most recent version of doctl or go to [Releases · digitalocean/doctl (github.com)](https://github.com/digitalocean/doctl/releases)
```powershell
Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-windows-amd64.zip -OutFile ~\doctl-1.110.0-windows-amd64.zip
```
2. extract the file, by running 
```powershell
Expand-Archive -Path ~\doctl-1.110.0-windows-amd64.zip
```

4. open **terminal** in administrator and running the following command to 
	1. create a  directory in program file folder, 
	``` powershell
	New-Item -ItemType Directory $env:ProgramFiles\doctl\
	```
	2. move the unpack file to the new directory
	``` powershell
	Move-Item -Path ~\doctl-1.110.0-windows-amd64\doctl.exe -Destination 
	```
	3. add set the environment path, ``````
		``` powershell
		[Environment]::SetEnvironmentVariable(
		    "Path",
		    [Environment]::GetEnvironmentVariable("Path",
		    [EnvironmentVariableTarget]::Machine) + ";$env:ProgramFiles\doctl\",
		    [EnvironmentVariableTarget]::Machine)
		$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
		```

note : make sure that if you are using window download the file that is name `doctl-version-windows-architechure-zip`)
##### Step 2: Create an API token 
1.  go to [DigitalOcean | Cloud Infrastructure for Developers](https://www.digitalocean.com/) then login or signup 

2. on the home page click **API** ![[Pasted image 20240917185310.png]]

3. then click **generate token** ![[Pasted image 20240917185825.png]]

4. type the token name and choose the expiration and scopes
	 - token name :  choose the name of the token
	- Expiration : choose when the token expires, it will make the token unable to authenticate to API after the interval passes
	- Scope : choose the permission that allow token to do
		- custom scope : let you specific the scope from the full list of scope
		- read scope :  grant the permission to read for all resource
		- full scope : grant all permission
	
![[Pasted image 20240917190812.png|450]]
4. copy the the token (warning! token only show once)
![[Pasted image 20240917192752.png]]

#### Step 3 : Authenticate `doctl` by using  API 

1. open **powershell app**
2. to Initializes authentication and give the authentication name
``` bash 
doctl auth init --context <NAME>
```
3. then paste the token that you copy from API page

note : you can add multiple account and switch authentication account by using `doctl auth switch --context <NAME>` and `doctl auth list` to see the list of authentication you have

##### to Confirm `doctl` is working

after run the authentication, run `doctl account get`. The result should look like this: 
![[Pasted image 20240917195939.png]]



### Create Droplet using `doctl`
---
##### to upload the custom image 
1. open **power shell**
2. to access digital ocean account, run :
```bash
	doctl auth init your_profile_name
```
3. to add the custom image
	```
	doctl compute image create <image-name> [flags]
	```
	- `<image-name>` name your image
	- `[flags]` additional commands(optional)
		- `--image-url` URL of the image file.
		- `--region` is a flag used to specify the region.
		- `--description` is a flag used to provide a description of the image.
	command `doctl compute create` require `--region, --image-url`
example
```bash 
doctl compute image create  arch_linux --image-url https://gitlab.archlinux.org/archlinux/arch-boxes/-/package_files/7527/download --region nyc1
```



##### to create Droplet 
1. open **power shell**
2. to create droplet run:
	``` bash
doctl compute droplet create <name> --image <image> --size <size>
	```
	- `<image>` is ID or slug of the image
	- `<size>` is the size of the VCPU and ram in form of `s-[number]vcpu-[number]gb`
	note : you may require to add SSH using additional command : `--ssh-keys <keyid>`, since image might require the SSH key


##### Additional commands 
- `doctl compute image list` show the list of images 
- `doctl compute droplet list` show the list of droplet 
- `doctl compute droplet delete <dropletID>` delete droplet 
- `doctl compute image list <imageID>` delete image 
- `doctl compute ssh-key list` show the list of SSH keys
`



### source
---

SolarWinds. (n.d.). _Using journalctl: The ultimate guide to logging_. Loggly. [https://www.loggly.com/ultimate-guide/using-journalctl/](https://www.loggly.com/ultimate-guide/using-journalctl/)

[How to Destroy a Droplet from the DigitalOcean Control Panel | DigitalOcean Documentation](https://docs.digitalocean.com/products/droplets/how-to/destroy/)

[How to Upload Custom Images | DigitalOcean Documentation](https://docs.digitalocean.com/products/custom-images/how-to/upload/)

[How to Create Droplets from Custom Images | DigitalOcean Documentation](https://docs.digitalocean.com/products/custom-images/how-to/create-droplets/)

[doctl compute image | DigitalOcean Documentation](https://docs.digitalocean.com/reference/doctl/reference/compute/image/)

[doctl compute droplet | DigitalOcean Documentation](https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/)

[doctl compute droplet create | DigitalOcean Documentation](https://docs.digitalocean.com/reference/doctl/reference/compute/droplet/create/)