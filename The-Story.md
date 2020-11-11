# Story

When I first began trying to get up and running my own dynamic cloud hacking setup, I noticed that the array of tools and ecosystems were so large, and there were 50 different ways to do just about everything, do I use ansible for provisioning on server boot, do I load ansible with packer? How much do I configure for image builds? There were a few ‘red team’ infra setup tools and aids, but they all required so much legwork just to get off the ground. It felt like in a lot of cases people were just publishing what they use without any help/documentation on getting started.

The other situation I faced, when looking at other pentesting distros, is that they had very little support for a lot of the common tools I was using in my day-day bug bounty and red team work. Distro’s such as Kali were great for traditional netsec, but for bug bounty and large-infrastructure projects they lacked a lot of the great stuff

Specifically Go tools, lots of really awesome small Go utilities such as the array of masterpieces from likes of [Tom Hudson](https://github.com/tomnomnom/), [Luke Stephens](https://github.com/hakluke) and [Jason Haddix](https://github.com/jhaddix). Bug bounty has become overrun with fancy and clever Go utilities usually stitched together in bash one liners.

Setting up your own ‘hacking vps’, to catch shells, run enumeration tools, scan, let things run in the background in a tmux window, used to be an afternoon project. You would run through and install all the tools you need manually, configure your ZSH, configure vim, configure tmux.

With Axiom, you just need to run a single command to get setup, and then you can use the Axiom toolkit scripts to spin up and down your new hacking VPS.

![https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled.png](https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled.png)

Run `axiom-init` and watch as a new instance is created in under 2 minutes containing everything you could ever want or need to run your reconnaissance for your pentest, catch a shell in netcat, or maybe you want to VPN through (axiom comes with support for one-click deployment profiles for things like openvpn, `axiom-deploy openvpn` and you soon have a fully configured openvpn server.

When you’re finished, simply bring down the instance with `axiom-rm your-instance-12` a quick confirmation dialog, and your box is gone! It’s no longer costing you anything to run.

![https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%201.png](https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%201.png)

The init script, packed with `notify-send` hooks, can be run entirely headlessly while it spins up your machine of choice.

![https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%202.png](https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%202.png)

In this toolkit, I have attempted to make setting up your own cloud hacking box as simple as possible with as little touch from you as is necessary.

To aid you, I have created an array of bash wrappers to get started. The axiom base image has been developed with bug hunters and lean teams to quickly initialize and dispose of infrastructure (and actually have the tools that they use daily, preinstalled).

![https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%203.png](https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%203.png)

`axiom-ssh host` is used to connect to your machines, to see which machines you have available, use `axiom-ls`

![https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%204.png](https://raw.githubusercontent.com/pry0cc/axiom/master/screenshots/Untitled%204.png)