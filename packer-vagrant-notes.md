# Starting to play with packer and vagrant

The bottom line is I want Packer to build Centos 7 boxes from scratch and
configure and use them thanks to Vagrant as a throwaway Hadoop cluster for dev.
I want all of this to (ultimately) not rely on the network so I have full
control of what goes in, and I can do this when offline. There is still a long
way to go to that end, but here are the first steps. We don't want to depend on
images downloaded from the Internet but only on the original ISO from Centos,
and the Cloudera rpms.

## Starting with vagrant

Vagrant allows to build virtual machine images and provision the VMs through
some scripting.

I started using <https://github.com/theclue/cdh5-vagrant> as a base, but had to
  make various modifications:

- disabled Flume possibly missing some config and taking 100% CPU at startup
- fixed some misconfig related to the latest Hue.ini version
- changed memory sizes for VMs
- updated for Centos 7 + JDK 8, instead of 6 + 7 to match my planned
  architecture

The (WIP) result is at <https://github.com/dmorel/cdh5-vagrant>.

At that stage, I needed a manually built Centos VM as a base(that I called
Centos7-vagrant in Virtualbox), which I built using the instructions on
<https://blog.engineyard.com/2014/building-a-vagrant-box>. I put its name in
the Vagrant file, and used the following commands to build and power up the
cluster:

```
vagrant package --base Centos7-vagrant # VM name in VirtualBox
vagrant box add centos7 package.box    # register the resulting image
vagrant init centos7                   # build the cluster
vagrant up                             # fire up the 3-machine cluster
```

## Next step: automated Centos7 build with Packer

I didn't want to depend on a box file taken off the web (or even look for one)
and wanted an automated way to build a Centos 7 box file. I found this one which
looked OK: <https://github.com/geerlingguy/packer-centos-7>

It relies heavily on Ansible for quite a few config tasks, I have a feeling
this could also be done, or at least large parts of it, using packer directly.
I don't mind since I'm starting to use Ansible anyway. Also, it uses some
external scripts for roles, downloading them from Ansible Galaxy. I should
retrieve these and modify/include them locally.

I changed a few things to test + try and understand how the whole thing works,
most notably the vagrant post-processor, because I made the (possibly false)
assumption that all things vagrant should happen in the other (the
cdh5-vagrant) tree. We'll see, but for now it's a 2-step process with some
manual operations involved.

The result is at <https://github.com/dmorel/packer-centos-7> and it works like
this:

- build a vagrant-ready centos VM in
  `./output-virtualbox-iso/packer-centos-7-x86_64.ovf`
```
packer build --only=virtualbox-iso centos7.json 
```

- import it in VirtualBox manually, but this should also work:
```
VBoxManage import ./output-virtualbox-iso/packer-centos-7-x86_64.ovf
```

- cd to the cdh5-vagrant repo dir, and run the vagrant commands from the
  previous section (the Vagrantfile in the repo has been updated to use the 
  result of this packer build, meaning the name of the VM once imported in 
  VirtualBox)

- done

## Still plenty to do

- import/modify the remote ansible scripts (they're separate github repos,
  too)
- decide where customization should happen: in the packer build, or the vagrant
  file later? We may want to keep a generic enough Centos 7 build, matter of
  taste
- fix some issues regarding package installation (when / where should it
  happen? remove dupes)
- investigate the vagrant post-processor, maybe (probably) we can do all of
  this in 1 step; this (probably) means merging the 2 repos, which will otoh
  render tracking the origin on github impossible. Not sure what the best
  practices here are
- use a local yum repo
- etc.

## Thanks

I'm only being a user here, the hard work has been done by the 2 repo owners,
Gabriele Baldassarre and Jeff Geerling.

