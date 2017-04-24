Generic dev box

## Notes

* Notes and playbooks shall improve. But for now, be content with monthly/weekly virtualbox builds

Needs playbooks improvement:
- Needs to upgrade golang to latest version to install direnv properly
- Needs to upgrade postgres to 9.4 to enable jsonb for first-circle-app
- Needs to download first circle repo and prepare everything via the playbooks
- Remove digitalocean

## Preparation

Download Vagrant 1.9.3
Download VirtualBox 5.1.16 r113841

Copy the config:

```
cp box.yml{.sample,}
```

Edit the `box.yml` to pick your own settings for the virtual machine. `vagrant up` to start the VM.

```
ssh-add /path/to/your/private_key
ssh-copy-id -i /path/to/your/public_key.pub vagrant@ip.of.vagrant.box
```

`vagrant` user's password is `vagrant`.

## Hosts file

Copy `hosts.sample` to `hosts`:

```
cp hosts{.sample,}
```

Then edit the file to point to the IP of the virtual machine. Later, when running the playbooks, we will specify what group to use.

## Running Playbooks

All of the playbooks below are run like this:

```
ansible-playbook <playbook-name>.yml -i hosts -l <virtualbox or digitalocean>
```

```
ansible-playbook setup.yml -i hosts -l virtualbox -vvvv
```

### Setup

This should always be run.

Playbook name: `setup`

### PostgreSQL

Playbook name: `postgresql`

Make sure your `group_vars/development` the following settings:

```
postgresql_password: XXXXXXXXXX
postgresql_databases:
  - app_testdb
  - app_devdb
```

### Ember

Playbook name: `ember`

### Ruby

Playbook name: `ruby`. This installs RVM.

### Running a Rails App

in the box

```
rails s -p 5774 -b 0.0.0.0
```

in host

```
http://192.168.33.10:5774
```

### Exporting

```
vagrant package --debug
```

### Importing

```
vagrant box add fc_dev_box package.box
```

### After Importing

```
vagrant up
```

if error telling "default: Warning: Remote connection disconnect. Retrying.." ssh into
the box via ```vagrant ssh```. Then add your host id_rsa.pub to the ~/.ssh/authorized_keys file of guest

Delete your ~/.ssh/known_hosts file
