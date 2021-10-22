# Cerebro Puppet Module [![Puppet Forge](https://img.shields.io/puppetforge/v/jpveldhuizen/cerebro.svg?style=flat-square)](https://https://forge.puppet.com/modules/jpveldhuizen/cerebro)

#### Table of Contents

1. [Description](#description)
1. [Setup - The basics of getting started with Cerebro](#setup)
    * [Setup requirements](#setup-requirements)
    * [Beginning with Cerebro](#beginning-with-cerebro)
1. [Advanced usage - Configuration options and additional functionality](#advanced-usage)
    * [Connecting to multiple pre-configured clusters](#connecting-to-multiple-pre-configured-clusters)
    * [Proxyserver settings](#proxyserver-settings)
    * [Limiting access with basic or LDAP authentication](#limiting-access-with-basic-or-ldap-authentication)
1. [Reference - An under-the-hood peek at what the module is doing and how](#reference)
1. [Limitations - OS compatibility, etc.](#limitations)
1. [Development - Guide for contributing to the module](#development)

## Description

This is a basic Puppet module to install and set up cerebro.

[Cerebro](https://github.com/lmenezes/cerebro) is an open source elasticsearch web admin tool built using Scala, Play Framework, AngularJS, and Bootstrap. Cerebro is the follow up project to Kopf- a popular elasticsearch web admin plugin.

## Setup

### Setup requirements

Cerebro >= 0.9.0 requires Java 11. This module does not attempt to install Java for you.

### Beginning with Cerebro

This will install OpenJDK 11 and Cerebro version 0.9.4 with default settings.
```
# install java
package { 'openjdk-11-jdk-headless':
  ensure => present,
}

# install Cerebro with default values
class { 'cerebro': }
```

## Advanced usage

## Connecting to multiple pre-configured clusters

You can provide specific targets to connect to with an array of hashes:

```
class { 'cerebro':
  targets => [ { name => 'Production Cluster',
                 host => 'http://prod:9200',
               },
               { name => 'Dev Cluster',
                 host => 'http://dev:9200',
               }, ]
}
```

Or, through Hiera and Automatic Parameter Lookup:

```
cerebro::targets:
  - name: 'Production Cluster'
    host: 'http://prod:9200'

  - name: 'Dev Cluster'
    host: 'http://dev:9200'
```

### Proxyserver settings

You can provide a proxyserver url and type to download the original package.

```
class { 'cerebro':
  proxy_server => 'http://proxy.localnet:8080',
  proxy_type   => 'https',
}
```

### Limiting access with basic or LDAP authentication

You can require login with a basic challenge:

```
class { 'cerebro':
  basic_auth_settings => {
    username => 'admin',
    password => '1234',
  }
}
```

Or through LDAP:

```
class { 'cerebro':
  ldap_auth_settings => {
    url           => 'ldap://ldapserver.localnet:389',  # can also be 'ldaps://ldapserver.localnet:636' for example.
    base-dn       => 'ou=active,ou=Employee,dc=example,dc=com',
    method        => 'simple',  # can be 'simple' ,'simple_tls' or 'start_tls'
    user-template => '%s@example.com',
    bind-dn       => 'cn=ldap_reader,ou=active,ou=Employee,dc=example,dc=com',
    bind-pw       => 'T0ps3cr3t!', 
  }
  ldap_group_search_settings => {
    base-dn => "ou=active,ou=Employee,dc=example,dc=com",
    user-attr => "uid",
    user-attr-template => "%s",
    group => "(&(objectClass=user)(memberOf=cn=Admins,ou=Security Groups,ou=Employee,dc=example,dc=com))",
  }
}
```

## Reference

### Parameters

#### Class: `cerebro`

- `version`: Specify Cerebro version. Defaults to 0.9.4.
- `service_ensure`: Determines whether the cerebro service should be running. Defaults to running.
- `service_enable`: Determines whether the cerebro service should be enabled when the system is booted. Defaults to enabled.
- `secret`: Specify secret string. Defaults to a random 32 bits string.
- `hosts`: Specify a list of known hosts. None by default.
- `basepath`: Specify application base path.
- `shell`: Specify a shell for cerebro user.
- `manage_user`: Specify whether creating user that cerebro process is executed as. Defaults to enabled.
- `cerebro_user`: Specify the user that cerebro process is executed as. Defaults to 'cerebro'.
- `package_url`: Specify a package location wher to download from. Defaults to "https://github.com/lmenezes/cerebro/releases/download/v${version}/cerebro-${version}.tgz". 
- `java_opts`: Specify `JAVA_OPTS` variables.
- `java_home`: Specify `JAVA_HOME` path.
- `basic_auth_settings`: Specify basic authentication settings.
- `ldap_auth_settings`: Specify LDAP authentication settings.
- `ldap_group_search_settings` : Specify additional groupmembership filtering for LDAP.
- `address`: Specify IP address cerebro listening on. Defaults to none, listening on all available interfaces.
- `port`: Specify TCP port (1024-49151) Cerebro listening on. Defaults to port 9000.
- `proxy_server`: Full address with portnumber for the proxyserver to use. Defaults to none.
- `proxy_type`: type of proxyserver to use, can be one of "none", "http", "https" or "ftp". Defaults to none.


## Limitations

This module has been tested on:

- Ubuntu 20.04

## Development

Bug reports and pull requests are welcome on GitHub at https://github.com/jpveldhuizen/puppet-cerebro.
