The problem is not just with scanning images. Usually, we do scan the images manually OR in any CI/CD pipeline using trivy or clair. 

But it is not just the issue with STATIC CONTAINER SCANNING, but also the security has to be at runtime. 

For example, to check if a command has started a sudo OR su shell or the history has been deleted or a package manager command has been started in a pod / container. 

This is where once the image was scanned and was not found with any errors but the code is doing something wrong. In that case, we can use tools that can audit individual processes inside the containers. 

Here comes falco,

Falco is an audit and monitoring tool that uses system calls to secure and monitor a system by 

1. Parsing the system calls at runtime from kernel 
2. Asserts the call against a rule database
3. Alert when a rule is violated

It can check things like

1. A pod / container tried to escalate priv. 
2. Inside the container, namespace change was attempted using tools like setns.
3. Read / Write to well known directories such as /etc/, /usr/bin
4. Ownership and mode changes
5. Executing shell binaries such as sh, zsh or so
6. Executing SSH binaries such as scp, ssh, sftp and so on.
7. Modification of /etc/passwd


=====

Installation

Falco can be installed as a systemd service on a single node or in a container.

This git repo was written for 

1. OS: CentOS Linux release 7.8.2003 (Core) 
2. Falco Version: 0.27.0
3. Falco Driver Version: 5c0b863ddade7a45568c0ac97d037422c9efb750

[root@localhost ~]# git clone github.com/shkatara/falco_systemd.git
[root@localhost ~]# cd falco_systemd
[root@localhost ~]# mkdir ~/.falco
[root@localhost ~]# cp falco_centos_3.10.0-1160.11.1.el7.x86_64_1.ko ~/.falco/
[root@localhost ~]# systemctl enable --now falco 

=====

Verification

Once falco is up and running, we will start a container from ubuntu and start the package management process inside it to see the logs from falco.

[root@localhost ~]# docker run -d --name falco ubuntu sleep 1000000
[root@localhost ~]# docker exec -it falco sh
#apt
#exit
[root@localhost ~]# journalctl -u falco | grep -i package

It will give the events. 

=====

Configuration:

Falco comes up with its own rules database at /etc/falco/falco_rules.yaml


