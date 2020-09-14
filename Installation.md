To install axiom, all you have to do is ensure that you have all the right dependencies and then run the configuration script.

## Dependencies
- Digital Ocean API Key - (Personal Access Token) - https://cloud.digitalocean.com/account/api/tokens
- SSH Keypair (I recommend using a key without a passphrase for speed, even if used just for Axiom.)
- git
- curl
- jq - 1.6 
- packer - Tested with v1.5.6
- doctl 
- rsync
- lsb_release - Tested with 1.4 (but any version should be ok)
- fzf (If you want menus)

Once you have these all installed, run the following command and enter your DigitalOcean API key

## Easy Install 
```
bash <(curl -s https://raw.githubusercontent.com/pry0cc/axiom/master/interact/axiom-configure)
```

## Manual
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

# Troubleshooting
If even after all these steps, it still isn't working, then use this checklist:
- Does ~/.axiom/axiom.json exist? Is it empty?
- Is doctl configured? Run `doctl compute instances list` to test, you may need to run `doctl auth`. 
- Is axiom in your path? Run `echo $PATH`, your `$PATH` should contain ~/.axiom/interact/, this is the directory with all the scripts
- Are you using axiom correctly? Check the quick start guide!
