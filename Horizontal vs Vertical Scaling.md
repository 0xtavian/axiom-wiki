# Horizontal vs Vertical Scaling
While most modules are designed to scan a lot of targets, any module can be rewritten to scan one target with the combined power of the entire fleet.

With axiom-scan modules, most of the time we are splitting a target list (a bunch of IPs for example) and uploading parts of the target list to every instance, but if we wanted to do something like brute force one target with 5 axiom instances, we can do that by splitting a wordlist instead.

## Vertical Scaling - Many to Many

By rearranging the special `input` variable in the module to point to a wordlist instead of a target list, axiom-scan will split the word list and run it against the target you hardcoded in the module or specified inline.

For example: the following puredns module spits a target list and brute forces each target with the entire wordlist.

```
[{
    "command":"/home/op/go/bin/puredns bruteforce /home/op/lists/2m-subdomains.txt input | tee output",
    "ext":"txt"
}]
```
Example: `axiom-scan myrootdomains.txt -m puredns -o myresults`


Adding the  special `_wordlist_` variable in the module allows axiom-scan to change the wordlist if `-w` is present the command line but is not required. This is only included for example sake.

```
[{
    "command":"/home/op/go/bin/puredns bruteforce _wordlist_ input | tee output",
    "ext":"txt",
    "wordlist":"/home/op/lists/2m-subdomains.txt"
}]
```
Example: `axiom-scan myrootdomains.txt -m puredns -w /home/op/lists/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt -o myresults`

Both modules above are examples of vertical scaling. We take the targetlist e.g. `myrootdomains.txt`, split and upload parts of the target list to every instance and brute force the targets with the entire wordlist.


## Horizontal Scaling - Many to One
If we wanted to horizontally scale e.g targeting one host with the combined power of the entire fleet, lets look at the next example:
```
[{
    "command":"/home/op/go/bin/puredns bruteforce input tesla.com | tee output",
    "ext":"txt"
}]
```
Example: `axiom-scan bitquark-subdomains-top100000.tx -m puredns -o my-horizontal-results`

In the above example, we are hardcoding the target tesla.com directly into the module. More importantly, the special variable `input` is now positioned as a wordlist. When running the axiom-scan command we must pick a wordlist as our second positional argument to split and upload. <br>

Alternative you could have a module without the hardcoded target and instead specify the target in the command like.
```
[{
    "command":"/home/op/go/bin/puredns bruteforce input | tee output",
    "ext":"txt"
}]
```
Example: `axiom-scan bitquark-subdomains-top100000.tx -m puredns tesla.com -o myoutput.txt`

