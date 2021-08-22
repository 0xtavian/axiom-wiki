# Cheatsheet

A little cheatsheet to help you on your way!


---
# axiom-build 
**Pick a Packer provisioner** :hatching_chick:
> You must run axiom-configure before anything else. This creates your axiom.json file which is required by other commands.
```
axiom-build
```
> We currently offer [three](https://github.com/pry0cc/axiom/tree/master/images/provisioners) different base provisioner files, and 2 custom provisioner file to build images. 
> - [Default](https://github.com/pry0cc/axiom/blob/master/images/provisioners/default.json) is our latest iteration of axiom provisioners. It comes with all tools installed and has been optimized for readability. Recommended for axiom-feet.
> - [ReconFTW](https://github.com/pry0cc/axiom/blob/master/images/provisioners/reconftw.json) provisioner includes all tools in ReconFTW and works with all modules. Recommended for reconftw_axiom.sh. 
> - [Barebones](https://github.com/pry0cc/axiom/blob/master/images/provisioners/barebones.json) is our decently secure base install, comes with Docker, Go-lang, Interlace and nmap installed. Recommended for templating.

> Custom Images:
> - [Classic](https://github.com/pry0cc/axiom/blob/master/images/provisioners/classic.json) provisioner works with most modules and was the first iteration of axiom provisioner files.
> - [Light](https://github.com/pry0cc/axiom/blob/master/images/provisioners/light.json) provisioner comes with zsh and a few other minor adjustments over the barebones provisioner. 
---
* If you want to bring-your-own Packer JSON provisioner, select custom. Make sure your packer json is placed in ~/.axiom/images/provisioners
# axiom-init
**Initialize a single instance** :seedling:

```bash
axiom-init <name> # Init
axiom-init <name> --deploy=<profile> --restore=<box> # Init & deploy & restore 
#Note: profile argument is file name is without .json
```
> the `--deploy` option allows you to deploy a profile (stored in `~/.axiom/profiles/`). `--restore` will restore a profile. These options can be used together.

---
# axiom-ssh
**Connect to an instance** - axiom-connect**

```bash
axiom-connect #automatically connects to list initialized instance
axiom-connect <name>
axiom-ssh <name> <ssh args>
axiom-ssh <name> --tmux
axiom-ssh <name> -m -t=<tmux session> # Connect with mosh and attach to tmux session
axiom-ssh <name> -m -t=main-15
axiom-ssh <name> -L 8080:127.0.0.1:8080 -D 4040 # All additional SSH args are passed to SSH
axiom-ssh <name> --cache # Don't generate ssh config, just connect with cache
axiom-ssh --just-generate # Generate an SSH config at ~/.axiom/.sshconfig
```
---
# axiom-backup
**Backup an initialized instance to the cloud** :luggage:

```bash
axiom-backup 
```

> After running axiom-init you can set up config files, unique wordlists, private tools etc, take a snapshot of that image and use it to deploy future axiom instances/fleets. 

---
# axiom-sync
**Backup the home directory of an initialized instance to the local filesystem**

```bash
axiom-sync instance01
```

> This allows you to download a copy of everything in an axiom instance to the local filesystem

---

# axiom-boxes
**List all box backups** :brain:

```bash
axiom-boxes ls # List boxes
axiom-boxes ls --archive # List archived boxes
axiom-boxes new <box> # Create a blank box backup
axiom-boxes rm <box> # Delete a box backup 
axiom-boxes archive <box>  # Archive a box (tarball & gpg -e)
axiom-boxes unarchive <box> # Unarchive (from ~/.axiom/archives/), gpg decrypts
axiom-boxes get pry0cc/lazy # Get a box from  github, username/repo
```
> Using the archive option will convert the box to a tarball and encrypt using the email specified in your account `~/.axiom/accounts/*.json`

---

# axiom-region
**Switch to a new region** :compass:

```bash
axiom-region ls # lists regions
axiom-region select <region> # select region

axiom-region select ams2
```

---

# axiom-account
**Manage user accounts** 

```bash
axiom-account-setup # Setup a new account
axiom-account # No args, list available accounts
axiom-account <account> # Select/Switch to the specified account
```
---

# axiom-scp
**Copy files to and from hosts** 
> Copy a remote file from all instances in myfleet to local file. `'$name'` must be wrapped single quotes. `'$name'` is a required literal string and gets interpolated from the instance name. <br>

```
axiom-scp 'myfleet*':/home/op/myfile.txt '~/local/folder/$name.txt'
```
> Copy a remote folder from all instances in myfleet to local folder. `'$name'` must be wrapped single quotes. `'$name'` is a required literal string and gets interpolated from the instance name. <br>

```
axiom-scp 'myfleet*':/home/op/myfile/ '~/local/folder/$name'
```

> Copy a remote file from one instance 'myfleet05' to a local file <br>

```
axiom-scp myfleet05:/home/op/myfile-from-myfleet05.txt '~/local/folder/myfile-from-myfleet05.txt'
```

> Copy a local file to all instances in myfleet, for example uploading a custom wordlist  <br> 

```
axiom-scp myfile.txt 'myfleet*':/home/op/myfile.txt
```

> Copy a local folder to all instances in myfleet, for example uploading a folder of custom nuclei templates <br>

```
axiom-scp mytemplates/ 'myfleet*':/home/op/custom-templates 
``` 
---

# axiom-select
**Deploy a profile** :mechanical_arm:

```bash
axiom-select '<instance>'
axiom-select 'testy*'  # Testy is an example of a fleet
axiom-deploy openvpn # Install openvpn against host or fleet
axiom-deploy covenant # Install covenant
```

> All profiles can be found in `~/.axiom/profiles`

**Connect to a remote axiom instance Docker (over ssh)**

```bash
axiom-select <instance>
. axiom-docker # Not a typo, the . means source
docker ps
```
---
# axiom-vpn
**Connect to an OpenVPN Server Deployed by Axiom** 

```bash
axiom-vpn <instance>
```

---


# axiom-proxy
**Proxy through a set of hosts (or one)**
```bash
axiom-proxy '<fleet>*'
axiom-proxy '<instance>'
axiom-proxy 'testy*'
proxychains4 curl -s ipinfo.io
```
> This will SSH tunnel all hosts to a range of  local ports, 127.0.0.1:5000-50100, then generate a proxychains configuration file to `./proxychains.conf`

---

# axiom-dns
**Create a DNS A record**
*Currently only supported by Digital Ocean*
```bash
axiom-dns ls # List domains
axiom-dns ls <domain> # List records for domain
axiom-dns add <subdomain> <domain> <ip address>
axiom-dns add cisco navisec.xyz 167.71.89.136 # Example, this will create a record cisco.navisec.xyz pointing to 167.71.89.136 
```
> Domain names can be hosted in DigitalOcean ([https://cloud.digitalocean.com/networking/domains?](https://cloud.digitalocean.com/networking/domains?i=87a4bd))

---

# axiom-fleet
**Initialize a fleet** :rocket:  

```bash
axiom-fleet -i=13 # Initialize a fleet, name it randomly
axiom-fleet testy -i=8 # Initialize a fleet named 'testy', instances will be named, testy01, test02 etc
axiom-fleet testy -i=10 --regions=NYC1,LON1,TOR1 # Initialize a fleet using round-robin region distribution  
```

---

# axiom-scan
**Scanning using a fleet** :rocket: 
*Modules are in `~/.axiom/modules/`*

```bash
axiom-select 'fleet*' # fleets should be already selected, but just in case, select can be a good idea

# Format
axiom-scan <input> -m <module> -o <text outfile> <any other args>

# Examples
axiom-scan subs.txt -m httpx -o http.txt # httpx module
axiom-scan http.txt -m nuclei -o nuclei.txt # nuclei module, find vulns
axiom-scan http.txt -m gowitness -o screenshots # gowitness, take screenshots
axiom-scan subs.txt -m dnsprobe -o dns.txt # Run dnsprobe

axiom-scan ips.txt -m nmap -oG portscan.txt # nmap
axiom-scan ips.txt -m nmap -oX portscan # Will create both portscan.xml & portscan.html
axiom-scan ips.txt -m nmap -oX full -p- -T5 -sV --script=vulners # Will create full.xml, will pass all args to nmap command

axiom-scan ips.txt -m masscan -oG masscan.txt # Run masscan
```
---


# axiom-exec
**Execute a command against an Instance** :robot:

```bash
axiom-exec '<command>' '<instance>' # Execute a command against an instance
axiom-exec '<command>' '<instance>' --cache # Execute a command, use cached connection
axiom-exec '<command>' '<instance>' -q --cache  # Execute silent, just show command output

axiom-exec 'nmap -T5 navisec.io' 'testy01' -q --cache  # Example
axiom-execb 'nmap -T5 navisec.io' 'testy01' -q --cache
```
