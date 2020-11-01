Once you have a fleet spun up, and it is selected (follow the fleets guide), you can use axiom-scan. `axiom-scan` is a tool that allows you to perform scan-like operations across your fleets. It always, at a minimum, requires a single argument, this argument should be an input file accessible in the local directory with a list of targets.

What is inside your input file will depend on the type of scan you want to run. You can modify the behaviour of a scan by using different axiom-scan modules, these are located in `~/.axiom/interact/modules/` and are simple JSON files that detail the behaviour of your scan.

## Scanning at scale using Axiom - Stök
[![IMAGE ALT TEXT](http://img.youtube.com/vi/7ogiwKaIvxw/0.jpg)](http://www.youtube.com/watch?v=7ogiwKaIvxw "Scanning at Scale - STÖK")


# NOTICE:
For version 2, new syntax is coming, the only change syntactically will be the output modes -oX and -oG accordingly, as well as the removal of "=" for variable assignment. A new syntax command for v2 looks as follows:

```
axiom-scan roots.txt -m subfinder -o subs.txt
axiom-scan subs.txt -m dnsprobe -o dns.txt

cat dns.txt | awk '{ print $2 }' | anew ips.txt

cat ips.txt | cf-check > ips.txt

axiom-scan ips.txt -m masscan -oX masscan.xml -rate=100000
axiom-scan ips.txt -m nmap -oG nmap.txt -T4 -p- -sV
axiom-scan ips.txt -m nmap -oX nmap.xml -T4 -p- -sV

ports.py nmap.xml | anew hosts.txt

axiom-scan hosts.txt -m httpx -o http.txt

axiom-scan http.txt -m gowitness -o screenshots
axiom-scan http.txt -m ffuf -o content.csv
```

As of right now, the below modules are  as follows:
```
amass.json     gau.json       masscan.json   nmapx.json     subfinder.json
dnsprobe.json  freestyle.json httpx.json     nmap.json      nuclei.json    zmap.json
```

# `axiom-scan` - Performing distributed scanning, some examples.
To perform a simple masscan across a list of IP addresses, run:

```
axiom-scan ips.txt -p80,443
```

You can set an outfile also:

```
axiom-scan ips.txt -p80,443 -o=masscan.txt
```

Any argument supplied to axiom-scan will automatically be passed to the underlying command, such as:

```
axiom-scan ips.txt -p80,443,8080 --rate=100000 --banners -o=masscan-banners-fast.txt
```


# Modules
You can use axiom-scan modules using the `-m=module` flag. Let's demo running subfinder against a list of domains.

```
axiom-scan domains.txt -m=subfinder -o=subf.txt
```

Assuming your list of domains is greater than the number of instances in your fleet, it will split the domains up however many times you have instances in a fleet,  if you have 3 instances, it will split your infile 3 ways and upload them all.

Once uploaded, it will run your module code, download the output, and then sort and merge the output into a single outfile. Neat huh?

```
axiom-scan subsf.txt -m=httpx -o=http.txt
```

Using the above command we can run httpx against our subdomains, and get a list of URLS, nice!

Given some work, the above commands can be used either on the fly, or in a bigger automation script, let your mind run wild!

## Example Axiom-scan modules
`masscan.json`
```
{
	"command":"sudo masscan $args -p$ports -iL $infile -oG $outfile && sudo chown op:users $outfile",
	"box_infile":"input",
	"box_outfile":"output",
	"output_dirs":"false",
	"output_ext":"txt",
	"output_format":"greppable",
	"default_ports":"80,443",
	"default_args":"--rate=100000"
}
```

`httpx.json`
```
{
	"command":"cat $infile | /home/op/go/bin/httpx $args > $outfile",
	"box_infile":"input",
	"box_outfile":"output",
	"output_format":"txt",
	"output_ext":"txt",
	"output_dirs":"false",
	"default_ports":"",
	"default_args":""
}
```

## Nmapx with HTML output
A slightly cool feature of the axiom-scan tool is the nmapx module, given a list of ip addresses & ranges, this will distribute nmap across your fleet and output the results to a bootstrap themed html outfile! This can be really pretty and really intuitive way to read your scan results, especially across a lot of hosts.


```
axiom-scan 100.txt -m=nmapx -T5 -sV -p80 -o=scan
```

This will perform a distributed nmap service scan against port 80, and give me a HTML output to read my results!

## Distributed ffuf
this is coming, but will be a feature for premium users. This is a very potentially dangerous module and releasing it to the public could result in mass-DDoS type events, especially on bounty programs. I want to make sure we have a way to control distributed content discovery scans, so that we don't end up creating a weapon of mass destruction. Axiom is basically a botnet for pennies on the dollar (with predictable billing).  