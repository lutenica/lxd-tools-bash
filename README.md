# lxd-kit
The purpose of this project is to contain tools that are used for automating LXD management.

It also contains the required debian config to package as .deb.

## containomancer
### Description
This script helps us launch containers using predefined network configurations, such as IP address, resolvers as well as a hostname. To do this we utilize LXD's support for cloud-init. The way that would work is by creating profiles, particularly passing data in the user.networkconfig and user.user data sections of the yaml used to configure the individual profile. All of that is great but can not be adjusted at launch time as lxd/c has no vision inside the sections in question, it just passes them on to cloud init running in the container. This means we need some sort of wrapper script like this, which would appear to be on the fly for the end user. 

The tool uses yaml configs to add at runtime to service the user.network-config and user.user-data sections of the default profile. Thusly it manages to combine both the default profile settings, say for CPU cores, or ram and our custom networking stuff which LXC can't easily set with flags.

The standard configs are placed in /usr/local/etc/lxdkit_default_data.yaml and /usr/local/etc/lxdkit_default_net.yaml (net is for network, data for user data). You can edit these so for example you install additional applications, or add paths to your own at runtime using the flags descr
ibed below. The default files (or provided paths to them) are needed in order for this to actually run. It will use these to create the temp files that actually get passed along to lxd and once done it will remove the temp files.

It is important to use cloud based images as those come with support for cloud init. Otherwise this won't work. 

### How to use?

By default you will get the whmcs skeleton which will install less, virt-what  and lsb-release. 

The script takes the following arguments:

-f - FQDN of the container.

-t - Target cluster member node. This is optional. You need to provide the full name as shown in "lxc cluster list".

-i - IP address to be used.

-r1 - Resolver (Optional, will default to 1.1.1.1 if not supplied).

-e - Interface to use (This is for VMs as they like to default to enp5s0).

-o - OS image (Optional, defaults to debian/10/cloud). Note that the image you supply must be of.
       the /cloud variety, as this uses coud-init and custom non-networky stuff won't happen.

-v - Flag for launching VM rather than container.

-n - Optional, provide path to the networking yaml config you've created. Defaults to /usr/local/etc/lxdkit_default_net.yaml

-c - Optional, provide path to the user.user-data config you've created. Defaults to /usr/local/etc/lxdkit_default_data.yaml

-d - Deletes the specified containers as well as profiles, it will try to delete a container with the name being the localpart of the fqdn provided in the -f argument"
     You can just use "containomancer -f $name -d" and it will delete it. $name can be an fqdn or just the name in lxd.

-h - print help message

example:

containomancer -f local.doamin -i 10.0.0.4 -g 10.0.0.1"

## supdate
### Description
Script that edits hosts file to block snap updates.
Takes following arguments:

  -d - Disables snap updates altogether by blocking in hosts file.
       Will also set the snap autoupdate timer to what is specified
	   via -t option.

  -e - Enable snap updates, reversing the modification. Does not touch
       update timer.
  
  -t - Time at which autoupdates would occur. This is simply so that
       it doesn't check 1000 times while disabled. Please follow the format
	   given here https://snapcraft.io/docs/keeping-snaps-up-to-date

  -u - Enables updates, runs snap refresh and then disables them. Run this
       when we want to upgrade LXD basically.
 
  -h - Print usage
