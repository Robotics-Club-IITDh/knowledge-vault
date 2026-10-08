apt is the command you’ll use to install everything. You’ll be using it quite often so it’s a good idea to know how it works.
Do read the whole whole following document as it will be useful for your whole Linux journey.

If you want to install something, say ros, you just open your terminal and type

```bash
sudo apt install ros-jazzy-desktop-full
```

And it will handle the installation for you.

APT stands for Advanced Package Tool.
You’ll see the command everywhere. You may also see apt-get, which is just an older version of apt (worse also) that has been kept in because people are used to it.

But apt is not an installer. apt is a manager.
And to learn what it does exactly, and how to keep your system from having random errors, let’s learn a bit about whats under the hood.

All software you install has a bunch of information, and they all rely on other software to work.
They are structured something like this:

```jsx
Package: libc6
Version: 2.35-0ubuntu3.4
Depends: libgcc-s1, libcrypt1
Conflicts: libc6-i386
Provides: libc
```

They have their name, of course. And they also have a list of dependencies - software that they rely on to work.

Now what happens if you just install everything you see is that some software may be dependant on a specific package, say abcd-v1.2, while some other software may be dependant on a newer version, say abcd-v2.1, and cant run on the older version.
What apt does is that it makes a map for itself, of all the packages that are needed. And it chooses the version which would be compatible with all. That’s its main job. 

It then runs another tool, **dpkg**. This is the installer. 
dpkg is what installs packages. apt manages and decides what to install, and gives a command to dpkg to install the specific version that was chosen.

Now when you give a command to install something, say ros, it looks something like this - 

```bash
sudo apt install ros-jazzy-desktop-full
```

and this one command manages the whole installation. All you have to do is approve it and you’re done. 

But, it’s important to know how this works, and you’ll see why soon.
How does Ubuntu know - just by me telling it to install ros - where to install it from, what files, whether to trust the source, etc?
apt keeps a list of sources. “sources” meaning places where it will install packages from.
Typically in the directory :

```bash
/etc/apt/sources.list.d/
```

There are a bunch of files, each for a different app that lists out it’s sources 
These lists are never added automatically. 
The sources are basically a whitelist - places you tell Linux that it’s safe to install from.
When you tell apt to install something, it searches in these sources for it.
Some apps are downloaded using apt, but the source where they can be installed from is not included by default.
As such, you must add sources if they do not exist already. But since doing so gives permission to install from those sources, you must make sure to add only trusted ones.
These are called repositories (A repository is just a collection of structured information, in this case a list of places from where downloading is permitted.) or ppas (Personal Package Archive).
You can add sources using the following command, typically looking like :

```bash
sudo add-apt-repository 'deb http://repository_address'
```

It could also look like (you don’t have to understand this) :

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Here it adds the list directly to the sources folder instead of doing it through the add-apt-repository command. 

apt also maintains another list of sources, in 

```bash
/etc/apt/sources.list
```

You must not confuse it with the previously mentioned directory.
Functionally they do the same thing, but you’re best off just not touching this one, as it is a single file with no structure like the previous folder, and everything gets very messy.

Now what if you can’t find a repository online for what you want to download?
It will mostly be availible in binary or .deb files.

Binary files are ready to execute codes. They do not check for dependencies, etc. They just run.
If theres a problem, or conflicting softwares - they just crash.
And since they run directly, they are also less secure to run.

.deb files are files with code, and metadata (data about the code, dependencies, etc)
Deb files can be installed using dpkg or apt.
You can use dpkg to install something using :

```bash
sudo dpkg -i /path/to/your/package_name.deb
```

Or you can use apt : 

```bash
sudo apt install ./filename.deb
```

Using apt is always better. apt calls dpkg internally. 
Meaning the flow works as such :
apt checks everything and decides what is appropriate to install → dpkg installs it
Skipping the first step might result in conflicting dependencies, unresolved held files, etc.
You can also install .deb files by opening them in the default app installer software in Ubuntu, which calls apt internally anyways.

Use apt. Once you understand how it works, it’s great.

Now you’ve installed something. What after?
You need to make sure it doesn’t go out of date. You need to make sure it upgrades with everything else - kernels, drivers, etc (What are kernels and drivers? Find out if interested)

So we have the last set of important commands

```bash
sudo apt update
```

This command updates the whole map, and lists of dependencies. However that’s the only thing it does. It does not download newer versions, only checks if there are newer versions.

Then there’s 

```bash
sudo apt upgrade
```

This is the command that downloads newer versions.
However, it only downloads newer versions. It does not delete or change any of the previous ones.
And that always leads to something breaking in the long run.

So there’s this command

```bash
sudo apt full-upgrade
```

This can make changes, delete, install, etc.
For the most part, it’s safe. 
But if you have added any external repos/ppas then it might mess with it.
That’s your job to handle  :) 
You need to figure out when to use normal upgrade, and when full-upgrade.
And what to do before and after using full-upgrade to make sure you don’t lose anything.

Okay, the final command 

```bash
sudo apt autoremove
```

This removes all the files you don’t need any more. 
It’s good practice to run it once in a while, and its good practice to run update and upgrade almost anytime you’re doing something new.

Okay that’s it. We’re done with this.
Yes , it was long. But it’s important to learn this so that you don’t mess anything up during installation and have your screen turn purple and freeze, causing you to be scared of Linux.
Until you decide - No! I fear nothing. I will fix this and nothing can stop me. I never back down from a challenge!
Then you spend two weeks trying to fix it, only causing Windows and Ubuntu to both get erased and now you have to do everything from start.

Yes, personal experience.

Most of it comes down to making sure you dont mix different versions ( like both Ubuntu Noble and Jammy together ) in the source files.

Anyways, you can try what you’ve learnt (and also increase download speed) by doing this:

[apt-fast installation + etc](apt-fast%20installation%20+%20etc.md)