You can install tools either before or after spinning up your fleet (monkey patching). If you already have a fleet, simply execute `axiom-exec`. This also works for updating. For example, to update Nuclei templates run `axiom-exec "nuclei -update-templates"`. To install a package with apt-get execute `axiom-exec "sudo apt-get install [package-name]"`

Adding tools to the packer build is only sightly more complicated. First add the command(s) to install the tool(s) in the [full.json](https://github.com/pry0cc/axiom/blob/master/images/provisioners/full.json#L21) file under the `inline` object. The command(s) must be non-interactive, wrapped in double quotes and needs to end with a comma for all lines except the [last line](https://github.com/pry0cc/axiom/blob/master/images/provisioners/full.json#L184). Next run axiom-build to create the new packer image with your tools installed.

Creating a module is also easy. Lets use [DNSX](https://github.com/pry0cc/axiom/blob/master/modules/dnsx.json) for example. DNSX is a prime choice being the easiest to work with. Its easy because it outputs to plaintext, which needs to be expressed [here](https://github.com/pry0cc/axiom/blob/master/modules/dnsx.json#L3) and the results are returned one line per target but dont need to be in any specific order, making it easy to merge.

```
[{
	"command":"cat input | /usr/bin/dnsx -silent -r /home/op/lists/resolvers.txt -o output",
	"ext":"txt"
}]
```

With axiom-scan, every instance executes the same command, which can be found in the module. axiom-scan splits the user provided target list (bunch of IPs for example) into equal parts per instance. Meaning if you have 5 instances, one target list is [divided](https://github.com/pry0cc/axiom/blob/master/interact/axiom-scan#L305-L350) into 5 equal parts (the best it can) and uploads them to the appropriate instance. During this process, it renames all files to `input`. Each instance is leveraging a file called `input` which is its portion of the target list. This is important to know as all modules need to include this or else Axiom-scan wont know where to find the target list.  

Just as important as a file named `input` to be the module, we also have `output`, which you guessed, is the filename for output file. To download results from each instance, axiom-scan uses rsync via SSH to download a file named `output` from each instance. The `output` files are then merged into one and finally renamed to a user provided argument.

`"cat input`, which as we previously explained is a portion of the target list, is passed to the binary `/usr/bin/dnsx` with arguments  `-silent -r /home/op/lists/resolvers.txt` and outputs the contents to a file named output `-o output"`.

