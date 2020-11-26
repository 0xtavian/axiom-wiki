# Cheatsheet

I've put together a little cheatsheet to help you on your way!

---

## Initialize a single instance

---

```bash
axiom-init <name> # Init
axiom-init <name> --deploy=<profile> --restore=<box> # Init & deploy & restore
```

the `--deploy` option allows you to deploy a profile (stored in `~/.axiom/profiles/`). `--restore` will restore a profile. These options can be used together.

---

## Connect to an instance

```bash
axiom-ssh <name> <ssh args>
axiom-ssh <name> --tmux
axiom-ssh <name> -m -t=<tmux session> # Connect with mosh and attach to tmux session
axiom-ssh <name> -m -t=main-15
axiom-ssh <name> -L 8080:127.0.0.1:8080 -D 4040 # All additional SSH args are passed to SSH
axiom-ssh <name> --cache # Don't generate ssh config, just connect with cache
axiom-ssh --just-generate # Generate an SSH config at ~/.axiom/.sshconfig
```

## Backup an Initialized Instance

```bash
axiom-backup <name> # Backup
```

Backups are stored in ~/.axiom/boxes, files to be backed up are in `~/.axiom/boxes/backup-files.txt.`

---

## List all box backups

```bash
axiom-boxes ls # List boxes
axiom-boxes ls --archive # List archived boxes
axiom-boxes new <box> # Create a blank box backup
axiom-boxes rm <box> # Delete a box backup 
axiom-boxes archive <box>  # Archive a box (tarball & gpg -e)
axiom-boxes unarchive <box> # Unarchive (from ~/.axiom/archives/), gpg decrypts
axiom-boxes get pry0cc/lazy # Get a box from  github, username/repo
```

Using the archive option will convert the box to a tarball and encrypt using the email specified in your account `~/.axiom/accounts/*.json`

---

## Manage user accounts

```bash
axiom-account-setup # Setup a new account
axiom-account # No args, list available accounts
axiom-account <account> # Select/Switch to the specifed account
```

---

## Initialize a fleet

```bash
axiom-fleet testy -i=8 # Initialize a fleet named 'testy', instances will be named, testy01, test02 etc
axiom-fleet -i=13 # Initialize a fleet, name it randomly
```

---

## Copy files to and from hosts

```bash
axiom-scp testy01:~/path/to/file output/test.txt # copy file from testy01:~/path/to/file to output/test.txt
axiom-scp 'testy*':~/path/to/file 'output/$name.txt' # Copy a file from every single instance in the testy fleet from the path to the name of the instance, $name gets interpolated. 
axiom-scp test.txt 'testy*':/path/to/file #  Copy test.txt to the same path on each fleet
```

---

## Deploy a profile

```bash
axiom-select '<instance>'
axiom-select 'testy*'  # Testy is an example of a fleet
axiom-deploy openvpn # Install openvpn against host or fleet
axiom-deploy covenant # Install covenent
```

All profiles can be found in `~/.axiom/profiles`

---

## Connect to an OpenVPN Server Deployed by Axiom

```bash
axiom-vpn <instance>
```

---

## Connect to a remote axiom instance Docker (over ssh)

```bash
axiom-select <instance>
. axiom-docker # Not a typo, the . means source
docker ps
```

---

## Proxy through a set of hosts (or one)

```bash
axiom-proxy '<fleet>*'
axiom-proxy '<instance>'
axiom-proxy 'testy*'
proxychains4 curl -s ipinfo.io
```

This will SSH tunnel all hosts to a range of  local ports, 127.0.0.1:5000-50100, then generate a proxychains configuration file to `./proxychains.conf`

---

## Create a DNS A record

```bash
axiom-dns ls # List domains
axiom-dns ls <domain> # List records for domain
axiom-dns add <subdomain> <domain> <ip address>
axiom-dns add cisco navisec.xyz 167.71.89.136 # Example, this will create a record cisco.navisec.xyz pointing to 167.71.89.136 
```

Domain names can be hosted in DigitalOcean ([https://cloud.digitalocean.com/networking/domains?](https://cloud.digitalocean.com/networking/domains?i=87a4bd))

---

## Scanning using a fleet

Modules are in `~/.axiom/modules/`

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

## Switch to a new region

```bash
axiom-region ls # lists regions
axiom-region select <region> # select region

axiom-region select ams2
```

---

## Execute a command against an Instance

```bash
axiom-exec '<command>' '<instance>' # Execute a command against an instance
axiom-exec '<command>' '<instance>' --cache # Execute a command, use cached connection
axiom-exec '<command>' '<instance>' -q --cache  # Execute silent, just show command output

axiom-exec 'nmap -T5 navisec.io' 'testy01' -q --cache  # Example
axiom-execb 'nmap -T5 navisec.io' 'testy01' -q --cache
```