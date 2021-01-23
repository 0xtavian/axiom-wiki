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
axiom-scp wordlist.txt jerry:~/wordlist.txt
```

Axiom will relay this data onto `rsync` with compression to move the file. How it works under the hood is really cool, so I suggest you check it out :D

## `axiom-restore` 
TBD