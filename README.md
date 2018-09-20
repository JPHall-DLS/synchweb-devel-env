# SynchWeb Virtual Development Environment

## Introduction
This project allows you to generate a vagrant based dev environment.

It uses Ansible for provisioning.

It can generate:
- a standalone machine with apache, php, synchweb and mariadb in one box.
- a multimachine setup:
  web: apache, php and synchweb
  db: mariadb server
  cas: nfs and cas authentication (via nginx and tomcat).

The test data includes a couple of visits registered to the user:password 'boaty':'mcboatface'

Note the standalone version is not compatible with CAS because of https port conflicts between nginx and apache
 
## Setup
* Install vagrant and ansible 
* Decide which option you want (multi_machine or standalone)
* cd into the dir (e.g. centos_standalone)
* Run vagrant up
* This should download the centos box and provision the machine(s)
* Try using a web browser on http://localhost:9080 or http://192.168.33.10 to see the Synchweb pages
* The SynchWeb source code will be synchronized to src sub directory (e.g. centos_standalone/src)

### Authentication
* Two authentication types are supported: dummy and cas. 
    * Dummy authentication should be used in the standalone case, CAS works for multi machine.
    * Before running vagrant up, edit the template file playbooks/roles/webserver/vars/main.yml
* Some features may require your host being able to resolve the hostname of the boxes (e.g. cas logout will redirect to https://cas/cas/logout) so add an entry in your hosts file to point to 192.168.33.12
* The CAS auth needs some work. At the moment it relies on a patched source file class.auth-cas.php to explicitly set the CAS certificate. The webserver auth_host variable should match the cas role sitename.

#### LDAP
SynchWeb can be configured to talk to an LDAP server.
An LDAP role is not currently included here.
However you can get one from here https://github.com/rgl/ldap-vagrant.git
Change the SynchWeb config.php settings to talk to the ldap server e.g. ldap://192.168.33.xx (see ldap Vagrant box for ip addr).
You can add users (e.g. boaty) into the LDAP provision.sh script. Best to do this before bringing the box up.

## Reprovision
* If you need to re-run the provisioning (after a change) run vagrant provision OR vagrant <boxname> provision for multi-machine

## Cleanup
* cd into the dir and run vagrant destroy
* This should cleanup and delete the vagrant box

## Notes
* We don't have many template files so some are stored under roles/<role>/files
* Could be moved to separate templates folder at a later date...
* There are lots of sql files (under roles/dbserver/files) that can be imported into the db - they will need checking to make sure you get what you need.
* The debian standalone has some issue with the nfs role - it requires a restart for it to mount properly. May be a service order issue, if in doubt try re-provisioning (vagrant up --provision).

## Web based Admin VM
* There is also a simple web based admin tool under centos_ispyb_admin/
* This is a flask tool that pulls code from https://github.com/drnasmith/flask_ispyb_admin
* To run the admin VM; cd into centos_ispyb_admin and run vagrant up.
* Its playbook is stored within the centos_ispyb_admin folder
* Once provisioned, source /home/vagrant/env/bin/activate, export FLASK_APP=app.py, then run: flask run --host 192.168.33.20
