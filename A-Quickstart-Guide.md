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
> - [Default](https://github.com/pry0cc/axiom/blob/master/images/provisioners/default.json) is our latest iteration of axiom provisioners. It comes with all tools installed and has been optimized for readability. Recommended for axiom-feet.
> - [ReconFTW](https://github.com/pry0cc/axiom/blob/master/images/provisioners/reconftw.json) provisioner includes all tools in ReconFTW and works with all modules. Recommended for reconftw_axiom.sh. 
> - [Barebones](https://github.com/pry0cc/axiom/blob/master/images/provisioners/barebones.json) is our decently secure base install, comes with Docker, Go-lang, Interlace and nmap installed. Recommended for templating.

> Custom Images:
> - [Classic](https://github.com/pry0cc/axiom/blob/master/images/provisioners/classic.json) provisioner works with most modules and was the first iteration of axiom provisioner files.
> - [Light](https://github.com/pry0cc/axiom/blob/master/images/provisioners/light.json) provisioner comes with zsh and a few other minor adjustments over the barebones provisioner. 
---
* If you want to bring-your-own Packer JSON provisioner, select custom. Make sure your packer json is placed in ~/.axiom/images/provisioners
---

# axiom-init
**Initialize a single instance** :seedling:

```bash
axiom-init <name> # Init
axiom-init <name> --deploy=<profile> --restore=<box> # Init & deploy & restore 
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


# axiom-ssh 
**Connect to an instance - axiom-connect** :link:

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
