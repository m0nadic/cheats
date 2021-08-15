# Chef Server

## Hosted chef server
* [Hosted Chef](https://manage.chef.io/)


```
$ knife client list
$ knife ssl check
$ knife cookbook list
```

## Upload a cookbook

```
$ knife cookbook upload apache
```
---

## Multi node vagrant setup

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

unless Vagrant.has_plugin?("vagrant-ohai")
  raise "vagrant-ohai plugin is not installed! Install with 'vagrant plugin install vagrant-ohai'"
end

NODE_SCRIPT = <<EOF.freeze
  echo "Preparing node..."
  # ensure the time is up to date
  yum -y install ntp
  systemctl start ntpd
  systemctl enable ntpd
  curl -L https://omnitruck.chef.io/install.sh | sudo bash -s -- -v 14
EOF

def set_hostname(server)
  server.vm.provision 'shell', inline: "hostname #{server.vm.hostname}"
end

Vagrant.configure(2) do |config|

 config.vm.define 'web1' do |n|
    n.vm.box = 'bento/centos-7.2'
    n.vm.box_url = 'https://vagrantcloud.com/bento/boxes/centos-7.2'
    n.vm.box_version = '2.3.1'
    n.vm.hostname = 'web1'
    n.vm.network :private_network, ip: '192.168.10.43', nic_type: "virtio"
    n.vm.provision :shell, inline: NODE_SCRIPT.dup
   set_hostname(n)
  end

 config.vm.define 'web2' do |n|
    n.vm.box = 'bento/centos-7.2'
    n.vm.box_url = 'https://vagrantcloud.com/bento/boxes/centos-7.2'
    n.vm.box_version = '2.3.1'
    n.vm.hostname = 'web2'
    n.vm.network :private_network, ip: '192.168.10.44', nic_type: "virtio"
    n.vm.provision :shell, inline: NODE_SCRIPT.dup
   set_hostname(n)
  end

 config.vm.define 'load-balancer' do |n|
    n.vm.box = 'bento/centos-7.2'
    n.vm.box_url = 'https://vagrantcloud.com/bento/boxes/centos-7.2'
    n.vm.box_version = '2.3.1'
    n.vm.hostname = 'load-balancer'
    n.vm.network "forwarded_port", guest: 80, host: 9000
    n.vm.provision :shell, inline: NODE_SCRIPT.dup
   set_hostname(n)
  end

end

```

---

## Bootstraping
Bootstrapping `web1`: From within the folder which has the `Vagrantfile` execute

```bash
$ vagrant ssh-config
```

Now from withing the chef repo directory in the vagrant host machine

```bash
$ knife bootstrap localhost                                                            \ 
                  --ssh-port <Port-from-ssh-config-cmd-output>                         \
                  --ssh-user <User-from-ssh-config-cmd-output>                         \
                  --sudo --ssh-identity-file <IdentityFile-from-ssh-config-cmd-output> \
                  -N web1
```


```bash
$ knife node list
$ knife node show web1
```

## Adding run list to node

```bash
$ knife node run_list add web1 "recipe[apache]"
```

Now login to `web1`

```bash
$ vagrant ssh web1
```

and execute 

```bash
[vagrant@web1 ~]$ sudo chef-client
```

## Supermarket

[Supermarket](https://supermarket.chef.io/)

## Creating wrapper cookbook

within chef-repo

``bash
$ chef generate cookbook cookbooks/lbhaproxy
```

edit `cookbooks/lbhaproxy/metadata.rb` file

and add the following line 

```ruby
depends 'haproxy', '~> 12.2.0'
```

```bash
$ berks install
$ berks upload
```
