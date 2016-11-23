Auto File
=========

Place the files that your super privileged container needs on the host file
system in the /exports/hostfs path and let `af` do its magic.

`af` explores the /exports/hostfs path in the container, builds an intermediate
rpm package and installs the package on the host.


Commands
--------
 * install   - install the given container's files to the host

 * uninstall - remove the given container's files from the host

 * query     - search for owner of the given file

 * list      - list all files delivered by the given container


How to test
-----------

Prepare a docker container in a terminal:

```bash
sudo docker run -it --rm --name wether fedora sh
mkdir -p /exports/hostfs/opt/filak/
echo "Hello, world!" > /exports/hostfs/opt/filak/jakub.txt
```

Run `af` in an other terminal to check which files the container provides:

```bash
sudo ./af list --container wether
```

If the output looks sane, install the files on the host:

```bash
sudo ./af install --rpm wether
```

Print out list of the installed files:

```bash
sudo ./af list wether
```
```
/opt/filak
/opt/filak/jakub.txt
```

Query container owning a file:

```bash
sudo ./af query /opt/filak/jakub.txt
```
```
wether
```

Use `rpm` to get information about the owner of a file:

```bash
rpm -qf /opt/filak/jakub.txt
```
```
C_wether___docker.io-fedora-latest-0.noarch
```

More details about the source container are include in the package description:

```bash
rpm -qfi /opt/filak/jakub.txt
```
```
Name        : C_wether___docker.io-fedora
Version     : latest
Release     : 0
Architecture: noarch
Install Date: Mon 14 Nov 2016 03:11:20 AM CET
Group       : Unspecified
Size        : 14
License     : None
Signature   : (none)
Source RPM  : C_weter___docker.io-fedora-latest-0.src.rpm
Build Date  : Mon 14 Nov 2016 03:11:20 AM CET
Build Host  : 4d7d6e02109a
Relocations : (not relocatable)
URL         : https://github.com/jfilak/af
Summary     : Host files from docker.io/fedora:latest
Description :
Files delivered by Docker container : wether
The container was created from Docker image : docker.io/fedora:latest
```

We might be enable to define dependencies between containers using the
`Provides` tag:

```bash
rpm -q --provides C_wether___docker.io-fedora-24-0
```
```
C_wether___docker.io-fedora = 24-0
spc(docker:docker.io/fedora:24)
```

Finally uninstall the files delivered by the container:

```bash
sudo ./af uninstall wether
```

Atomic host does not ship with rpm-build and it is not possible to install an
rpm package without several hacks. But nothing is impossible. The following
lines will `ostree admin unlock` your atomic machine and install the container
rpm package:

```bash
$ docker run -it --privileged --rm --pid=host -v /:/host --name builder fedora sh
$ dnf install -y rpm-build git
$ cd /tmp/
$ git clone https://github.com/jfilak/af
$ cd af
$ PATH="atomic-host:$PATH" ./af install --rpm wether
$ exit
$ rpm -qf /opt/filak/jakub.txt
```
```
C_wether___docker.io-fedora-latest-0.noarch
```

If Atomic gets `rpmbuild`, `af` will not need to be run in a container.

Disclaimer
-----------
Be sure the script can destroy your machine. Run it on your own risk.
Tested on Fedora Rawhide (F26) with docker-1.12.3-5.git9a594b9.

Author
------
Jakub Filak <jfilak@redhat.com>
