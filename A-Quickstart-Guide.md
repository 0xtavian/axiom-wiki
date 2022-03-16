So, you have axiom installed, you're feeling pretty good about yourself, you're 10 minutes away from feeling the power of axiom!

### Ensure your build has occurred successfully
If you've followed the installation steps with success, you should have run the [`axiom-configure`](https://github.com/pry0cc/axiom/wiki/A-Quickstart-Guide#axiom-configure) script at some point. axiom-configure automatically starts [`axiom-build`](https://github.com/pry0cc/axiom/wiki/A-Quickstart-Guide#axiom-build). During this time, [Packer by HashiCorp](https://www.packer.io/) will create an image. You should notice "Shutting down gracefully... Creating snapshot....".

You should have a valid build in your account now, if, in doubt, you can run [`axiom-build`](https://github.com/pry0cc/axiom/wiki/A-Quickstart-Guide#axiom-build) and create a new base snapshot just in case!

You can also manually edit your account details such as API key, image id, CPU, RAM etc, in the profile.json you just created `cat ~/.axiom/accounts/personal.json`. 

If you have a valid build in your account, skip to [`axiom-init`](https://github.com/pry0cc/axiom/wiki/A-Quickstart-Guide#axiom-init).




# axiom-configure 
**Install axiom** <br>
```
axiom-configure
```
You can provide your config inline or after running `axiom-configure`. <br>

`
axiom-configure --config { "do_key": "[redacted]", "region": "nyc1", "provider": "do", "default_size": "s-1vcpu-1gb", "appliance_name": "", "appliance_key": "", "appliance_url": "", "email": "", "op": "[redacted]", "imageid": "axiom-default-myimageid", "provisioner": "default", "sshkey": "id_rsa" }
`
<br>
>  To get your account.json config file in this format run: `cat ~/.axiom/accounts/do.json | tr '\n' ' ' | tr -s ' '`

---
# axiom-build 
**Pick a Packer provisioner** :hatching_chick:
> You must run axiom-configure before anything else. This creates your axiom.json file which is required by other commands.
```
axiom-build default
```
You can provide your provisioner inline or after running `axiom-build`.

> We currently offer [three](https://github.com/pry0cc/axiom/tree/master/images/provisioners) different base provisioner files, and 2 custom provisioner file to build images. 
> - [Default](https://github.com/pry0cc/axiom/blob/master/images/provisioners/default.json) is our latest iteration of axiom provisioners. It comes with all tools installed and has been optimized for readability. Recommended for axiom-fleet.
> - [ReconFTW](https://github.com/pry0cc/axiom/blob/master/images/provisioners/reconftw.json) provisioner includes all tools in ReconFTW and works with most modules. Recommended for ReconFTW. 
> - [Barebones](https://github.com/pry0cc/axiom/blob/master/images/provisioners/barebones.json) is our decently secure base install, comes with Docker, Go-lang, Interlace and nmap installed. Recommended for templating.

> Custom Images:
> - [Classic](https://github.com/pry0cc/axiom/blob/master/images/provisioners/classic.json) provisioner works with most modules and was the first iteration of axiom provisioner files.
> - [Light](https://github.com/pry0cc/axiom/blob/master/images/provisioners/light.json) provisioner comes with zsh and a few other minor adjustments over the barebones provisioner. 
---
* If you want to bring-your-own Packer JSON provisioner, select custom. Make sure your packer json is placed in ~/.axiom/images/provisioners
---

# axiom-images
**Manage images/snapshots with axiom-images** :cd:
#
<details>
  <summary>axiom-images usage</summary>
  
  ``` 
Examples: axiom-images ls && axiom-images rm axiom-barebones-1634682130 && axiom-images use axiom-default-1634682131
 Usage:
 ls                                      List snapshots created
 get                       Display info about the current image
 use | set              Use snapshot for axiom-init/axiom-fleet
 rm                                Remove snapshot from account
 help | --help | -h                        Print this help menu
  ```
</details>

```bash
axiom-images ls # List available images
axiom-images use <image name> # Replace image used for axiom-init/axiom-fleet with user provided image name 
axiom-images rm <image name> # Delete image by name
```
---

# axiom-init
**Initialize a single instance** :seedling:
#
<details>
  <summary>axiom-init usage</summary>
  
  ``` 
Description:
  Initialize one axiom instance with differnet options, such as image, region, size and axiom deployment profile
Examples:
  axiom-init # provision instance with random name
  axiom-init --deploy desktop # provision instance with random name, then deploy axiom profile 'desktop'
  axiom-init testy01 # provision instance named testy01
  axiom-init stok01 --region nyc3 --image axiom-barebones-1635920849 --size s-1vcpu-2gb --deploy desktop --shell
Usage:
  <name> string (optional)
    Name of the instance, supplied as a positional first argument
  --image <image name>
    Manually set the image to use (default is imageid in ~/.axiom/axiom.json)
  --region <region>
    User specified region to use (default is region in ~/.axiom/axiom.json)
  --deploy <profile>
    Deploy a profile after initialization (e.g desktop, openvpn, bbrf, wireguard)
  --shell (optional)
    Connect to instance after initialization
  --size <vm size>
    VM size to use  (default is size in ~/.axiom/account/account.json)
  --no-select (optional)
    Dont select instance after initialization (default is to select instance)
  --domain <example.com>
    Manually specify the domain to use (default is specified by cloud provider)
  --restore <backup>
    Initialize with a previous backup
  --help
    Display this help menu
  ```
</details>

```bash
axiom-init <name> # Init
axiom-init <name> --deploy <profile> --restore <box> # Init & deploy & restore 
#Note: profile argument is file name is without .json
```
> the `--deploy` option allows you to deploy a profile (stored in `~/.axiom/profiles/`). `--restore` will restore a profile. These options can be used together.
 


```
axiom-init
```

You can run this command without any parameters, and it will pick a name for you. This name is picked at random from a list of legends!

```
# axiom-init <instance name>
axiom-init jerry
```

If you supply an argument, it will be used as the instance name, in the above example we spin up an instance called "jerry".

# axiom-ls

Now you have spun up an instance, you can run `axiom-ls` and get a list of all running instances.
### List your instances -  `axiom-ls`

---


# axiom-exec
**Execute a command against an Instance** :robot:
#
<details>
  <summary>axiom-exec usage</summary>
  
  ``` 
Description:
  Evaluate shell one-liners or execute single commands on one or more instances in parallel                                                                                                                       
  Expand local and remote environment variables respectively                                                                                                                                                      
  Specify the fleet prefix, or let axiom use selected.conf by default (located in ~/.axiom/selected.conf)                                                                                                         
  Optionally execute command(s) in a detached tmux session on the remote instances (commands run in the background)                                                                                               
  Temporarily prevent axiom's SSH key regeneration and instead connect with a cached SSH config (default is ~/.axiom/.sshconfig)                                                                                  
Examples:                                                                                                                                                                                                         
  axiom-exec id # Execute command id across all instances currently selected.conf (located in ~/.axiom/selected.conf)
  axiom-exec ifconfig --fleet testy # Execute ifconfig on testy fleet. Automatically select all instances in fleet testy
  axiom-exec sleep "$(($RANDOM % 10))" "&& id &&" echo $HOSTNAME --fleet stok # Evaluate complex one-liners on fleet stok
  axiom-exec 'sudo apt dist-upgrade -y' -q --cache --fleet OtherFleet --tmux MySession01 # Quietly execute command(s) inside a detacted tmux session on the remote instances with custom session name
  axiom-exec whoami -q --cache --sshconfig ~/.axiom/log/exec/axiom-exec+1234567890/sshconfig --fleet oldfleet --tmux # Specify the axiom SSH config to use (default is ~/.axiom/.sshconfig)
Usage:
  <commands> required string
    Command(s) to run on the remote axiom instances, multiple commands can be wrapped in single or double quotes, but not required
  -f/--fleet <fleet prefix>
    Fleet prefix to execute on (default is ~/.axiom/selected.conf). Automatic wildcard support
  -i/--instance <instance name>
    Single instance to execute on
  --tmux <optional tmux session name>
    Execute commands in a detacted tmux session (commands run in the background). The default tmux session name is axiom-exec+$TIMESTAMP, or supply a custom tmux session name
  --sshconfig <sshconfig_file> (optional string)
    Path to axiom's SSH config (default is ~/.axiom/.sshconfig)
  -q/--quiet
    Disable progress bar, and reduce verbosity
  --debug
    Enable debug mode (VERY VERBOSE!)
  --cache
    Temporarily do not generate SSH config and instead connect with cached SSH config
  --logs
    Do not delete logs (logs will be stored in ~/.axiom/logs/exec/axiom-exec$TIMESTAMP)
  --help
    Display this help menu

  ```
</details>


```bash
  axiom-exec id # Execute command id across all instances currently selected.conf (located in ~/.axiom/selected.conf)
  axiom-exec ifconfig --fleet testy # Execute ifconfig on testy fleet. Automatically select all instances in fleet testy
  axiom-exec sleep "$(($RANDOM % 10))" "&& id &&" echo $HOSTNAME --fleet stok # Evaluate complex one-liners on fleet stok
  axiom-exec 'sudo apt dist-upgrade -y' -q --cache --fleet OtherFleet --tmux MySession01 # Quietly execute command(s) inside a detacted tmux session on the remote instances with custom session name
  axiom-exec whoami -q --cache --sshconfig ~/.axiom/log/exec/axiom-exec+1234567890/sshconfig --fleet oldfleet --tmux # Specify the axiom SSH config to use (default is ~/.axiom/.sshconfig)
```

# axiom-ssh 
**Connect to an instance - axiom-ssh** :link:
# 
<details>
  <summary>axiom-ssh usage</summary>
  
  ```
Description:
  axiom-ssh dynamically generates axiom's SSH config based on your cloud inventory.
  axiom-ssh allows you to connect to your axiom instances over their public or private network interface.
  axiom-ssh can drop you right into a freshly created tmux session on the remote instance, and can be used to
  attach to a preexisting tmux session.
  All additional SSH args are passed to SSH.
Examples:
  axiom-ssh testy01 # SSH into instance testy01
  axiom-ssh testy01 --tmux mysession1 # SSH into instance testy01 and spawn or attach to session named mysession1
  axiom-ssh --just-generate # Always populate axiom's ssh config (located in ~/.axiom/.sshconfig) with public Ip details
  axiom-ssh --just-generate private # Always populate axiom's ssh config (located in ~/.axiom/.sshconfig) with private Ip details
  axiom-ssh --just-generate cache # Never regenerate axiom's ssh config
  axiom-ssh testy01 -L 8080:127.0.0.1:8080 -D 4040  # Port-forward 8080 to local port 8080 and dynamically port foward port 4040 to testy01
Usage:
  <instance name> required string
    Instance name supplied as a positional first argument
  --mosh/-m <instance name> (optional)
    Connect with mosh
  --just_generate <public, private, cache> (optional)
    Specify when to generate the SSH config file and what IPs to use. Options are public, private, cache ( default is public )
  --tmux <tmux session name to create>
    Connect to your instance and start new tmux session. If you dont include a session name one will be chosen automatically for you
  --tmux-attach/-t <tmux session> (optional)
    Connect to your instance and attach to tmux session by name
  --cache (optional)
    Temporarily do not generate SSH config and instead connect with cached SSH config
  --help (optional)
    Display this help menu
  <additional args>
    All additional SSH args are passed to SSH. If you want additional arguments supplied to your command, simply append them to the command
    example: axiom-ssh <name> -L 8080:127.0.0.1:8080 -D 4040
  ```
</details>

```bash
axiom-connect #automatically connects to list initialized instance
axiom-connect <name>
axiom-ssh <name> <ssh args>
axiom-ssh <name> --tmux
axiom-ssh <name> -m -t <tmux session> # Connect with mosh and attach to tmux session
axiom-ssh <name> -m -t main-15
axiom-ssh <name> -L 8080:127.0.0.1:8080 -D 4040 # All additional SSH args are passed to SSH
axiom-ssh <name> --cache # Don't generate ssh config, just connect with cache
axiom-ssh --just-generate # Generate an SSH config at ~/.axiom/.sshconfig
```
---

### Connect to your instance - `axiom-ssh <instance>`
Now you have an instance, and it has a public IP address, you can connect to your new instance using  `axiom-ssh`

```
# axiom-ssh <instance>
axiom-ssh jerry
```

If you want to be dropped into a tmux session, that will remain after you disconnect, use the `--tmux` flag.

```
axiom-ssh jerry --tmux
```

To detach from this tmux session, run `ctrl+a d`, this will disconnect you from the box but leave your tmux session running (and any command running in that session!)


---
# axiom-backup 
**Backup an initialized instance to the cloud** :luggage:

```bash
axiom-backup 
```

> After running axiom-init you can set up config files, unique wordlists, private tools etc, take a snapshot of that image and use it to deploy future axiom instances/fleets. 

---

### Backup an Initialized Instance - `axiom-backup <instance>`
After running axiom-init you can set up config files, unique wordlists, private tools etc, take a snapshot of that image and use it to deploy future axiom instances/fleets.
```
axiom-backup jerry
```


---
# axiom-rm
**Remove one or more axiom instances** :x:

```bash
axiom-rm <name> # Delete box by specifying the name
axiom-rm <name> -f # Forcibly delete box by specifying the
axiom-rm <name>\* # Delete all instances that start with <name>. You must escape the asterisk.  
axiom-rm <name>\* -f # Forcibly delete all instances that start with <name>. You must escape the asterisk.  
axiom-rm '\*' # Delete all instances on the account. Must be wrapped in single quote and escape the asterisk. 
axiom-rm '\*' -f # Forcibly delete all instances on the account. Must be wrapped in single quote and escape the asterisk. 


```
---
### Delete your instance
Once you're done with your instance, delete it using the command, `axiom-rm <instance>`

```
axiom-rm jerry

# or 

axiom-rm jerry -f
```

The -f flag will not prompt on deletion, I generally use this, but if you want to be ultra sure omit the `-f` flag.

# More Info
In Summary, a few useful commands to get you up and running, to use basic instances, and interact with them. Please refer to [Filesystem Utilities](https://github.com/pry0cc/axiom/wiki/Filesystem-Utilities) for more details.
