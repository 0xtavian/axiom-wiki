# Horizontal vs Vertical Scaling with Axiom-scan
While most modules are designed to scan a lot of targets (vertical scaling), with some creatively, modules can be written to scan one target with the combined power of the entire fleet (horizontal scaling).

With axiom-scan [modules](https://github.com/pry0cc/axiom/wiki/Adding-Simple-Modules), most of the time we are splitting a target list (a bunch of IPs for example) and uploading parts of the target to every instance, but if we wanted to do something like brute-force one target with five axiom instances, we can do that by splitting a wordlist instead.

By rearranging the special `input` file in the module to point to a wordlist instead of a target list, axiom-scan will split the wordlist and run it against the target you hardcoded in the module or specified inline.

## Vertical Scaling - Many to Many

In [an ideal world](https://github.com/d3mondev/puredns/issues/13) <sup>[1](#idealworld)</sup>, <a name="reverse"></a> the following puredns module spits a target list of domains and brute-forces each domain with the entire wordlist. 


```
[{
    "command":"/home/op/go/bin/puredns bruteforce /home/op/lists/seclist/Discovery/DNS/dns-Jhaddix.txt --domains input --resolvers /home/op/lists/resolvers.txt | tee output",
    "ext":"txt"
}]
```
Example: `axiom-scan myrootdomains.txt -m puredns-bruteforce -o myresults` <br>

### `_wordlist_`
Adding the  special `_wordlist_` variable in the module allows axiom-scan to change the wordlist if `-w` is present the command line, but not required. This is only included to demonstrate the optional `_wordlist_` variable.

```
[{
    "command":"/home/op/go/bin/puredns bruteforce _wordlist_ input  --resolvers /home/op/lists/resolvers.txt | tee output",
    "ext":"txt",
    "wordlist":"/home/op/lists/seclist/Discovery/DNS/dns-Jhaddix.txt"
}]
```
Example: `axiom-scan myrootdomains.txt -m puredns -w /home/op/lists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -o myresults`

Both modules above are examples of vertical scaling. We take the target list of domains e.g. `myrootdomains.txt`, split and upload parts of the target list to every instance and brute-force the targets with the entire wordlist.


## Horizontal Scaling - Many to One
If we wanted to horizontally scale e.g targeting one host with the combined power of the entire fleet, lets look at the next example:
```
[{
    "command":"/home/op/go/bin/puredns bruteforce input tesla.com --resolvers /home/op/lists/resolvers.txt | tee output",
    "ext":"txt"
}]
```
Example: `axiom-scan bitquark-subdomains-top100000.tx -m puredns-single -o my-horizontal-results`

In the above example, we are hardcoding the target tesla.com directly into the module. More importantly, the special file `input` is now positioned as a wordlist. When running the axiom-scan command we must pick a wordlist as our first positional argument to split and upload. <br>

Alternative you could have a module without the hardcoded target and instead specify the target in the command line:
```
[{
    "command":"/home/op/go/bin/puredns bruteforce input --resolvers /home/op/lists/resolvers.txt | tee output",
    "ext":"txt"
}]
```
Example: `axiom-scan bitquark-subdomains-top100000.txt -m puredns-single tesla.com -o myoutput.txt` <br>
The above is a slightly modified version of this [puredns module](https://github.com/pry0cc/axiom/blob/master/modules/puredns-single.json).
The last two modules are examples of horizontal scaling. We take a wordlist, e.g. `bitquark-subdomains-top100000.txt`, split it and upload parts of the wordlist to every instance. All instances brute-force a singular target .e.g. `tesla.com`.

For another example of a horizontally scaling module take a look at [gobuster-dns](https://github.com/pry0cc/axiom/blob/master/modules/gobuster-dns.json).

<a name="idealworld"></a><sup>[1](#reverse)</sup> The working [puredns-bruteforce](https://github.com/pry0cc/axiom/blob/master/modules/puredns-bruteforce.json) is a one-shot module. Leveraging features provided by [Interlace](https://github.com/codingo/Interlace) to "turn single threaded command line applications into a fast, multi-threaded application". 
