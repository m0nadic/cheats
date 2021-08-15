# Chef

## Setup using Vagrant

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "bento/centos-7.2"


  config.vm.synced_folder "cookbooks/", "/home/vagrant/cookbooks", create: true

  config.vm.provision "file", source: "vimrc", destination: "$HOME/.vimrc"


  config.vm.provision "shell", inline: <<-SHELL
    curl https://omnitruck.chef.io/install.sh | sudo bash -s -- -P chefdk -c stable -v 0.18.30
    yum install nano -y
    yum install vim -y
  SHELL

end
```

now execute the following

```bash
$ vagrant up
$ vagrant reload
```

---

## Generate cookbook

```bash
$ chef generate cookbook cookbooks/apache
```

## Generate recipe

```bash
$ chef generate recipe cookbooks/apache server
```

## Create server recipe

edit file `~/cookbooks/apache/recipes/server.rb`

add the following content

```ruby
package 'httpd'

file '/var/www/html/index.html' do 
  content '<h1>Hello, world!</h1>'
end

service 'httpd' do
  action [ :enable, :start ]
end
```

## Apply recipe

```bash
$ sudo chef-client -z cookbooks/apache/recipes/server.rb
```

## Include recipe in the default recipe

edit `~/cookbooks/apache/recipes/default.rb` and add the following content.

```ruby
include_recipe 'apache::server'
```

## Apply the default recipe

```bash
$ sudo chef-client -z -r "recipe[apache]"
```

## Generate template

```bash
$ chef generate template cookbooks/apache index.html
```

## Use a template resource

edit `~/cookbooks/apache/recipes/server.rb` and change the content to

```ruby
package 'httpd'

template '/var/www/html/index.html' do
  source 'index.html.erb'
end

service 'httpd' do
  action [ :enable, :start ]
end
```
