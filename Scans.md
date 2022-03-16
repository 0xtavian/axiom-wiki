# `axiom-scan` - Performing Distributed Scanning

```
              _
  ____ __  __(_)___  ____ ___        ______________ _____                                                                                                                    
 / __ `/ |/_/ / __ \/ __ `__ \______/ ___/ ___/ __ `/ __ \                                                                                                                   
/ /_/ />  </ / /_/ / / / / / /_____(__  ) /__/ /_/ / / / /                                                                                                                   
\__,_/_/|_/_/\____/_/ /_/ /_/     /____/\___/\__,_/_/ /_/                                                                                                                    
                                                                                                                                                                             
                                    @pry0cc                                                                                                                                  
                                 & @0xtavian                                                                                                                                 
                                                                                                                                                                             
axiom-scan provides easy distribution of arbitrary binaries and scripts. 
axiom-scan splits and uploads user-provided input files (target lists), wordlists and configuration files to every instance.
axiom-scan executes the same command across all instances, which can be found in the module.
axiom-scan downloads and merges scan output in a variety of different ways, specified in the module (folder, merged text file, csv etc). 
individual scans are executed in the background, inside a unique tmux session and scan working directory on the remote instances
 
Usage:
  axiom-scan [flags]

Flags:
INPUT:
   string[]              the first positional argument must always be an input file, this can be a list of URLs, IPs, hostnames, etc
   --dont-split          do not split input file, upload entire input file to every instance (default is to split the input file)
   --dont-shuffle        do not randomize input file before uploading (default is to randomize)

MODULE:
   -m string[]           the axiom-scan module to use with the scan
   --list                print all available modules

WORDLIST:
   -w string[]                       replace the string _wordlist_ in a module with a user-provided wordlist (must be a path to a remote wordlist)
   -wL string[]                      replace the string _wordlist_ in a module with a user-provided local wordlist ( must be a path to a local wordlist)
   --nuclei-templates string[]       replace the string _wordlist_ in a module with a user-provided local folder
   -wD,--distribute-wordlist         requires -wL, when using -wL, split and upload local wordlist (default does not split the wordlist)

CONFIGURATIONS:
   --config string[]                 replace the string _config_ in a module with a user-provided configuration file (must be a configuration file on the remote instances)
   --local-config string[]           replace the string _config_ in a module with a user-provided local configuration file to upload ( must be a local configuration file)

OUTPUT:
   -o string[]          output as default (the first ext mentioned in the module) 
   -oD string[]         output as directory (must also be supplied in the module using "ext":"dir" or "ext":"")
   -oX string[]         output as XML/HTML (supported for nmap and masscan)(must also be supplied in the module using "ext":"xml")
   -oG string[]         output as greppable, merge and sort unique (must also be supplied in the module using "ext":"oG")
   -csv string []       output as csv, extract csv header, merge and sort unique (must also be supplied in the module using "ext":"csv")
   -anew string[]       pipe the output to anew before creating the final output file
   --quiet              do not display findings to terminal
   --rm-logs            delete remote and local logs after scan completes

FLEET:
   --fleet string[]            supply fleet prefix to use (default uses instances in /root/.axiom/selected.conf)
   --spinup int[]              number of instances to spin up prior to scanning (default uses instances in /root/.axiom/selected.conf)
   --rm-when-done              delete the selected instances after the scan completes
   --shutdown-when-done        shutdown the selected instance after the scan completes
   -F string[]                 path to custom SSH config file (default is located at /root/.axiom/.sshconfig)
   --cache                     do not regenerate SSH config prior to scan, instead use cached config (located at /root/.axiom/.sshconfig)

DEBUG:
   --debug              run with set -xv, warning: very verbose

EXTRA ARGS:
   string[]             supply additional arguments to be passed to the module

```

```
axiom-scan roots.txt -m subfinder -o subs.txt --threads 3
axiom-scan subs.txt -m dnsx -resp -o dns.txt 

cat dns.txt | awk '{ print $2 }' | anew ips.txt

cat ips.txt | cf-check > ips.txt

axiom-scan ips.txt -m masscan -oX masscan.xml -rate=100000
axiom-scan ips.txt -m nmap -oG nmap.txt -T4 -p- -sV
axiom-scan ips.txt -m nmap -oX nmap.xml -T4 -p- -sV

ports.py nmap.xml | anew hosts.txt

axiom-scan hosts.txt -m httpx -o http.txt

axiom-scan http.txt -m gowitness -o screenshots
axiom-scan http.txt -m ffuf -o content.csv --threads 2
```

To perform a simple masscan across a list of IP addresses, run:

```
axiom-scan ips.txt -p80,443
```

You can set an outfile also:

```
axiom-scan ips.txt -m masscan -p80,443 -o masscan.txt
```

Any argument supplied to axiom-scan will automatically be passed to the underlying command, such as:

```
axiom-scan ips.txt -m masscan -p80,443,8080 --rate=100000 --banners -o masscan-banners-fast.txt
```
Once you have a fleet spun up, and it is selected (follow the fleets guide), you can use axiom-scan. `axiom-scan` is a tool that allows you to perform scan-like operations across your fleets. It always, at a minimum, requires a single argument, this argument should be an input file accessible in the local directory with a list of targets.

What is inside your input file will depend on the type of scan you want to run. You can modify the behaviour of a scan by using different axiom-scan modules, these are located in `~/.axiom/modules/` and are simple JSON files that detail the behaviour of your scan.

## Scanning at scale using Axiom - Stök
[![IMAGE ALT TEXT](http://img.youtube.com/vi/7ogiwKaIvxw/0.jpg)](http://www.youtube.com/watch?v=7ogiwKaIvxw "Scanning at Scale - STÖK")



# Modules
You can use axiom-scan modules using the `-m module` flag. Let's demo running subfinder against a list of domains.

```
axiom-scan domains.txt -m subfinder -o subf.txt --threads 1
```

Assuming your list of domains is greater than the number of instances in your fleet, it will split the domains up however many times you have instances in a fleet,  if you have 3 instances, it will split your infile 3 ways and upload them all.

Once uploaded, it will run your module code, download the output, and then sort and merge the output into a single outfile. Neat huh?

```
axiom-scan subsf.txt -m httpx -o http.txt
```

Using the above command we can run httpx against our subdomains, and get a list of URLS, nice!

Given some work, the above commands can be used either on the fly, or in a bigger automation script, let your mind run wild!

## Example Axiom-scan modules
Examples can be read in ~/.axiom/modules/
#### Nmap
```
[
	{
		"command":"nmap -T4 -iL input -oG output",
		"ext":"txt"
	},
	{
		"command":"nmap -T4 -iL input -oX output",
		"ext":"xml"
	}
]
```
#### Distributed Gowitness (output is a directory, hence empty extension).
```
[{
	"command":"gowitness file -f input -P output",
	"ext":""
}]
```

#### Httpx
```
[{
	"command":"cat input | /home/op/go/bin/httpx -silent > output",
	"ext":"txt"
}]
```

## Nmap with HTML output
A slightly cool feature of the axiom-scan tool is the nmap module with output set with "-oX" (refer to examples above), given a list of ip addresses & ranges, this will distribute nmap across your fleet and output the results to a bootstrap themed html outfile! This can be really pretty and really intuitive way to read your scan results, especially across a lot of hosts.


```
axiom-scan 100.txt -m nmap -T5 -sV -p80 -oX scan.xml
```

This will perform a distributed nmap service scan against port 80, and give me a HTML output to read my results!

## Distributed ffuf
```
axiom-scan http.txt -m ffuf -o results.csv -threads 1

# -w specifies a custom wordlist to the absolute path on the axiom instance!
axiom-scan http.txt -m ffuf -w ~/lists/jhaddix-all.txt -o results.csv -threads 1
```

## Custom Wordlists
To specify a custom wordlist that is currently located on the axiom instance, use the `-w` flag with axiom-scan.
```
axiom-scan http.txt -m ffuf -w /path/on/instance -o ffuf.csv
```
This needs to be the absolute path of the file and the file must be present on the instance.

To upload a custom local file on scan start, use the `-wL` flag.

```
axiom-scan domains.txt -m shuffledns -wL ~/path/to/local/wordlist.txt -o shuffle.txt
axiom-scan http.txt -m ffuf -wL ~/path/to/local/wordlist.txt -o ffuf.csv
axiom-scan http.txt -m nuclei -wL ~/path/to/local/template.yml -o nuclei.txt
```
