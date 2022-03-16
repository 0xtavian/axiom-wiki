## Docker
This will create a docker container, initiate axiom-configure and axiom-build and then drop you out of the docker container. After a Packer image is successfully created, you will likely have to re-exec into your docker container.
```
docker exec -it $(docker run -d -it ubuntu) sh -c "apt update && apt install git -y && git clone https://github.com/pry0cc/axiom ~/.axiom/ && cd && .axiom/interact/axiom-configure"
```

## Easy Install 
As a standard user with root privileges, run this command on any [supported](https://github.com/pry0cc/axiom#operating-systems-supported]) OS. The script will prompt for sudo when root when required.
```
bash <(curl -s https://raw.githubusercontent.com/pry0cc/axiom/master/interact/axiom-configure)
```

## Using Git Clone
In most cases, the easy install should work, however, if you do want to install manually, this guide is your friend!

Ensure that ~/.axiom/ does not exist
```
rm -rf ~/.axiom/
```

Clone the axiom repository
```
git clone https://github.com/pry0cc/axiom ~/.axiom/
```

Run configure script
```
$HOME/.axiom/interact/axiom-configure
```
## Manual
To install axiom on an OS that is not on our [supported](https://github.com/pry0cc/axiom/blob/master/README.md#operating-systems-supported]) list, all you have to do is ensure that you have all the right dependencies and then run `axiom-account-setup`.

## Dependencies
- Digital Ocean API Key - (Personal Access Token) - https://cloud.digitalocean.com/account/api/tokens
- SSH Keypair (I recommend using a key without a passphrase for speed, even if used just for Axiom.)
- git
- curl
- ruby
- jq - 1.6 
- packer - Tested with v1.5.6
- doctl 
- Interlace - https://github.com/codingo/Interlace
- rsync
- lsb_release - Tested with 1.4 (but any version should be ok)
- fzf (If you want menus)

# Troubleshooting
If an error is occurring recently, please run axiom-update successfully before reporting an issue.

If even after all these steps, it still isn't working, then use this checklist:
- Does ~/.axiom/axiom.json exist? Is it empty?
- Is doctl configured? Run `doctl compute instances list` to test, you may need to run `doctl auth`. 
- Is axiom in your path? Run `echo $PATH`, your `$PATH` should contain ~/.axiom/interact/, this is the directory with all the scripts
- Are you using axiom correctly? Check the quick start guide!
- Is git complaining about un-mergable files on update? Delete the ones it complain about, then run the update again.
- Do you get an error about `functions.sh`? Run `axiom-account-setup`
---
### Error after updating
```
.axiom/interact/axiom-ls: line 5: /home/pry/.axiom/interact/includes/functions.sh: No such file or directory
```

### Fix
Run axiom-account to see what account profiles you have
```
axiom-account
```

Then run `axiom-account <profile>`
```
axiom-account personal
```


## FAQ
### I can't log in to my droplets, I get permission denied/wrong password?
Be default, axiom creates an SSH key named axiom_rsa in `~/.ssh/axiom_rsa`. The reference to the SSH key is stored in `~/.axiom/axiom.json`. You can change the `sshkey` value to another SSH key as long as its stored in `~/.ssh/`. You will need to rebuild (`axiom-build`) with your new SSH key to use it. 
### I spin up `X` instances but only `Y` provision, whats wrong?
Check that you havent exceeded your droplet limit. After a few billing cycles you can generally increase your droplet limit via opening a support ticket with the associated cloud provider 
