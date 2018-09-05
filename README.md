# puppet-on-centos
Module to setup puppet on Centos

## Setps to setup puppet in Master agent configuration on Your windows.
1. Clone the code to your local
2. Switch into the directory and run vagrant up 
3. This will bring up One centos vm with puppet server installed and One centos vm with puppet agent installed.
4. Update the /etc/hosts file on Puppet server with <Ip address of puppet server> <FQDN> <puppet as alias name>
5. Update the /etc/hosts file on Puppet agent with <IP address of puppet server> <puppet as alias name>

## Steps to install Jenkins on Agent node
1. On puppet master, create r10k directory at /etc/puppetlabs/r10k and then create r10k.yaml file in /etc/puppetlabs/r10k/r10k.yaml. 
 with content.
 
 ```yaml
 ---
:cachedir: /var/cache/r10k
:sources:
  :local:
    remote: https://github.com/manoharendla/r10k-site
    basedir: /etc/puppetlabs/code/environments
```
2. switch into directory /etc/puppetlabs/code/environments/production
3. r10k deploy environment -p. It clones the code from  "https://github.com/manoharendla/r10k-site"
4. create manifests directory in /etc/puppetlabs/code/environments/production
5. create a site.pp file in /etc/puppetlabs/code/environments/production/site.pp

```ruby
node 'centos7' {
  class { 'java' :
    package => 'java-1.8.0-openjdk-devel',
  }
  include jenkins
}
node default {
  notify { "Hi I'm node ${hostname}": }
}
```

## Existing bugs 
1. Puppet agent will fail with USB2.0 error.
2. Work around for this issue is to 
   i. Launch virtual box
  ii. Select the agent machine.
 iii. Select settings -> USB ports and uncheck the usb option.
3. vagrant up 

Note: Vagrant should be installed. 

[![Puppet Installtion on Centos](https://img.youtube.com/vi/VXO5TMQtWx4/0.jpg)](https://www.youtube.com/watch?v=VXO5TMQtWx4)
