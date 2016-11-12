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


Disclaimer
-----------
Be sure the script can destroy your machine. Run it on your own risk.
Tested on Fedora Rawhide (F26) with docker-1.12.3-5.git9a594b9.

Author
------
Jakub Filak <jfilak@redhat.com>
