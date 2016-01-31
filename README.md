## Ghost
Ghost is a simple publishing platform and very easy to deploy

the steps usually are,

- Download the latest release of Ghost
- Unzip in the location you want to install
- Fire up a terminal
- npm install --production
- Start Ghost!
- Local environment: npm start
- On a server: npm start --production
- http://localhost:2368/ghost :tada:

the problem however hosting is,

1- need to proxy 2368 to 80 for your webserver
2- npm start, is not a service or daemon.

we solve the two problems

1- using nginx to proxy the port
2- using pm2 to start or restart the dameon on demand.

## Ansible to the rescue
could we automate the entire build process? yes!! Here comes ansible!!
Ansible is a simple automation system. it has several use cases,
but in ours we need it to deploy Ghost. each **<role: []>** corresponds to a role created.

**roles: [common, epel, firewalld, ntp]**

1- install and configure server
- ntp
- epel
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

**role: [nodejs]**

4- install Node, NPM and related global packages (Grunt, pm2)

**role: [ghost]**

5- Deployment
- download ghost from source code
- npm install all pre-reqs
- grunt init and grunt prod
- create a pm2.production.js file for pm2

## Missing notes

1- how to run

```shell
$ ansible-playbook -i inventory/  ghost-server.yml -vvv
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
