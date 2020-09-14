So, you have axiom installed, you're feeling pretty good about yourself, you're 10 minutes away from feeling the power of axiom!

### Ensure your build has occurred successfully
If you've followed the installation steps with success, you should have run the `axiom-configure` script at some point. During this configuration, `packer` will be run and a machine snapshot will be created. You should notice "Shutting down gracefully... Creating snapshot....".

You should have a valid build in your account now, if, in doubt, you can run `axiom-build` and create a new base snapshot just in case!

### Initialize a new instance - `axiom-init`
Before you can interact with axiom, you first have to initialize a new instance. 

```
axiom-init
```

You can run this command without any parameters, and it will pick a name for you. This name is picked at random from a list of legends!

```
# axiom-init <instance name>
axiom-init jerry
```

If you supply an argument, it will be used as the instance name, in the above example we spin up an instance called "jerry".

### List your instances -  `axiom-ls`

Now you have spun up an instance, you can run `axiom-ls` and get a list of all running instances.

```
axiom-ls
```

This will list your running instances and a monthly-cost estimate for each instance.

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