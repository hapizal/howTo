#HowTo 
#Setup a Local YUM/DNF RPM Repository
_Notes :
This howTo tested in Anolis 8 OS or Centos 8

Step 1
Create a directory for the repository: Example, /var/repo
```
cd ~
mkdir /var/repo
```
Note: If you are sharing this with many users, you make want to do this as root and in the /var directory.

Step 2
Install the tool needed to create repos... execute under root
```
createrepo_c /var/repo
```

Step 3
Copy RPMs we want to put in the repo
```
cp /path/to/rpms/*.rpm /var/repo/
```

Step 4
Create the yum .repo, for example local.repo
before that I prever backup or move existing repo files to other directory and make /etc/yum.repos.d/

```
[my-rpms]
# No spaces allowed in that [repo-name] or you get a "bad id for repo" error
name=My RPMs $releasever - $basearch
baseurl=file:/var/cache/repo
enabled=1
metadata_expire=1d
gpgcheck=0
#gpgcheck=1
#gpgkey=file:///<path to GPG key>/key.asc
#gpgkey=https://<URL to GPG key>/key.asc
```
Save that.

Step 5
Test it!
```
yum update
or 
dnf update
```
