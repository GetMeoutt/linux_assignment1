

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
0. open **terminal** app

1. run the following command to download the most recent version of doctl or go to [Releases · digitalocean/doctl (github.com)](https://github.com/digitalocean/doctl/releases)( make sure that if you are using window download the file that is name `doctl-version-windows-architechure-zip`)
		`Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-windows-amd64.zip -OutFile ~\doctl-1.110.0-windows-amd64.zip`
		or you can specify the path that you want the file to be save 
		`Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-windows-amd64.zip -OutFile [path]\doctl-1.110.0-windows-amd64.zip`

2. extract the file, by running `Expand-Archive -Path ~\doctl-1.110.0-windows-amd64.zip`

3. open **terminal** in administrator and running the following command to 
	1. create a  directory in program file folder, `New-Item -ItemType Directory $env:ProgramFiles\doctl\`
	2. move the unpack file to the new directory `Move-Item -Path ~\doctl-1.110.0-windows-amd64\doctl.exe -Destination `
	3. add set the environment path, ``````
		```
		[Environment]::SetEnvironmentVariable(
		    "Path",
		    [Environment]::GetEnvironmentVariable("Path",
		    [EnvironmentVariableTarget]::Machine) + ";$env:ProgramFiles\doctl\",
		    [EnvironmentVariableTarget]::Machine)
		$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
		```

##### Step 2: Create an API token 
1.  go to [DigitalOcean | Cloud Infrastructure for Developers](https://www.digitalocean.com/) then login or signup 

2. on the home page click on **API** ![[Pasted image 20240917185310.png]]

3. then click on **generate token** ![[Pasted image 20240917185825.png]]

4. type the token name and choose the expiration and scopes
	token name :  choose the name of the token
	Expiration : choose when the token expires, it will make the token unable to authenticate to API after the interval passes
	Scope : choose the permission that allow token to do
		custom scope : let you specific the scope from the full list of scope
		read scope :  grant the permission to read for all resource
		full scope : grant all permission
	
![[Pasted image 20240917190812.png|450]]
4. copy the the token (warning! token only show once)
![[Pasted image 20240917192752.png]]

#### Step 3 : Authenticate `doctl` by using  API 

1. open **powershell app**
2. run the following command `doctl auth init --context <NAME>` to Initializes authentication and give the authentication name
3. then type the token that you copy from API page


note : you can add multiple account and switch authentication account by using `doctl auth switch --context <NAME>` and `doctl auth list` to see the list of authentication you have

####  Confirm `doctl` is working
1. after run the authentication, run `doctl account get`. The result should look like this: 
![[Pasted image 20240917195939.png]]



### Create Droplet using `doctl`
---
\

### source
---

SolarWinds. (n.d.). _Using journalctl: The ultimate guide to logging_. Loggly. [https://www.loggly.com/ultimate-guide/using-journalctl/](https://www.loggly.com/ultimate-guide/using-journalctl/)