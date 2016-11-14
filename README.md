Auto File
=========

Place the files that your super privileged container needs on the host file
system in the /exports/rootfs path and let `af` do its magic.

`af` explores the /exports/rootfs path in the container, builds an intermediate
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

Run `af` in an other terminal:

```bash
sudo ./af list --container wether
sudo ./af install --rpm wether
sudo ./af list wether
sudo ./af query /opt/filak/jakub.txt
rpm -qf /opt/filak/jakub.txt
sudo ./af uninstall wether
```

Atomic host does not ship with rpm-build and it is not possible to install an
rpm package without several hacks. But nothing is impossible. The following
lines will `ostree admin unlock` your atomic machine and install the container
rpm package:

```bash
docker run -it --privileged --rm --pid=host -v /:/host --name builder fedora sh
dnf install -y rpm-build git
cd /tmp/
git clone https://github.com/jfilak/af
cd af
PATH="atomic-host:$PATH" ./af install --rpm whether
exit
rpm -qf /opt/filak/jakub.txt
```


Disclaimer
-----------
Be sure the script can destroy your machine. Run it on your own risk.
Tested on Fedora Rawhide (F26) with docker-1.12.3-5.git9a594b9.

Author
------
Jakub Filak <jfilak@redhat.com>
