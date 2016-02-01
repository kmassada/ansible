## Laravel
Laravel is a very complete php platform.

the steps usually are,

- Download composer
- install laravel using composer
- serve /public folder

Hosting laravel is fairly simple, but here are a few things we keep in our minds.

1- composer install sometimes craps out because of api limits
2- /storage has to be writable by your web user
3- keep an eye on cgi.fix_path = 1 /etc/php.ini


## Ansible to the rescue
could we automate the entire build process? yes!! Here comes ansible!!
Ansible is a simple automation system. it has several use cases,
but in ours we need it to deploy Laravel. each **<role: []>** corresponds to a role created.

**roles: [common, epel, firewalld, ntp]**

1- install and configure server
- ntp
- epel
- remi
- DevTools
- hostname setting
- firewalld

**role: [nginx]**

2- install and configure web server
- nginx
- nginx proxy config

**role: [mongodb, mariadb]**

3- install and configure database sources
- mongodb
- mariadb

**role: [php-fpm]**

4- install php-fpm, composer

**role: [laravel]**

5- Deployment
- download ghost from source code using composer
- set nginx and php-fpm settings

## Missing notes

1- how to run

```shell
$ ansible-playbook -i inventory/  laravel-server.yml -vvv
```

2- inventory/
```
$ ls inventory/
all      amazon   digital
```

I've excluded the ips of my servers to save myself from spam.
the simple config `[amazon:vars]` is a neat trick I learned @IRC ##ansible, for all amazon servers, in this case only have one, there's a key, and a user, so I can run `ansible-playbook` without having to specify inventory otherwise.

```
$ cat inventory/amazon
[amazon]
hostname.whatever.com

[amazon:vars]
 ansible_connection=ssh
 ansible_user=<my user>
 ansible_ssh_private_key_file=<full path>
```

3- valuts

/vault contains files with passwords. we've learned with vulnerabilities that no encryption is safe, at least to truly put it online. thanks to 'mysql_user' provided by ansible's extra modules, passwords can be set and reset at will. so I constantly change the password. on top of not exposing the db outside, and using 4096 bit rsa file to encrypt it.
