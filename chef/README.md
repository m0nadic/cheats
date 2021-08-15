# Chef

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

file '/var/www/html/index.html' do content '<h1>Hello, world!</h1>'
end

service 'httpd' do
  action [ :enable, :start ]
end
```
