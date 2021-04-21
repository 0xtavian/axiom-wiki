So I have my axiom instance, how do I get my files onto my instance, say a custom wordlist? 

This is a good question and a common problem, and one I have tried really hard to solve. That means there are a few answers to this.

## Using `axiom-scp`
If you've ever used `scp`, you'll know it makes moving files between local and remote machines a low-effort task. 

`axiom-scp` attempts to mimic this usefulness while abstracting away a lot of things.

Assuming your instance is called `jerry`, and you can see it in `axiom-ls`, and assuming you want to move a local file "wordlist.txt" to the home directory of our jerry instance.

```
# axiom-scp <local src file> <instance>:<full remote path>
# axiom-scp <instance>:<full remote path> <local file destination>
```

```
# axiom-scp wordlist.txt jerry:~/wordlist.txt
```

To copy a file from every single instance to the local file system you can use a wildcard. You must specify an output directory. $name is required and is a literal string that gets interpolated. 
```
# axiom-scp '<instance>*':/full/path/to/file 'output/$name.txt'
```

To copy a file or folder from the local file system to every instance, for example a folder of nuclei templates, simply specify the local path you wish to copy, the fleet prefix+asterisk wrapped in quotes followed by a colon and full path to the remote instance
`'buck*':/home/op/custom-templates`.  
```
# axiom-scp mytemplates/ 'buck*':/home/op/custom-templates
```

Axiom will relay this data onto `rsync` with compression to move the file. How it works under the hood is really cool, so I suggest you check it out :D

## `axiom-restore` 
TBD