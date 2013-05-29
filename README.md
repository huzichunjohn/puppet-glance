# PuppetLabs Glance module

This module can be used to flexibly configure [glance](http://glance.openstack.org/),
the image service of openstack.

It has been tested with a combination of other modules, and has primarily been
developed as a subcomponent of the [openstack module](https://github.com/stackforge/puppet-openstack)

It is currently targettting the folsom/grizzly releases of OpenStack.

## Platforms

* Ubuntu 12.04 (Precise)
* RHEL 6

## configurations

Glance is configured with the following classes:


### configures glance::api service

    class { 'glance::api':
      verbose           => $verbose,
      debug             => $verbose,
      auth_type         => 'keystone',
      auth_port         => '35357',
      auth_host         => $keystone_host,
      keystone_tenant   => 'services',
      keystone_user     => 'glance',
      keystone_password => $glance_user_password,
      sql_connection    => $sql_connection,
      enabled           => $enabled,
    }

### configures the glance registry

    class { 'glance::registry':
      verbose           => $verbose,
      debug             => $verbose,
      auth_host         => $keystone_host,
      auth_port         => '35357',
      auth_type         => 'keystone',
      keystone_tenant   => 'services',
      keystone_user     => 'glance',
      keystone_password => $glance_user_password,
      sql_connection    => $sql_connection,
      enabled           => $enabled,
    }

### Configure file storage backend

    class { 'glance::backend::file': }

### Create the Glance db, this should be configured on your mysql server

    class { 'glance::db::mysql':
      user          => $glance_db_user,
      password      => $glance_db_password,
      dbname        => $glance_db_dbname,
      allowed_hosts => $allowed_hosts,
    }

### configures glance endpoints in keystone
should be run on your keystone server

    class { 'glance::keystone::auth':
      password         => $glance_user_password,
      public_address   => $glance_public_real,
      admin_address    => $glance_admin_real,
      internal_address => $glance_internal_real,
      region           => $region,
    }

for full examples, see the examples directory.

in the module, puppet-openstack, the following classes
configure parts of glance:

  - openstack::glance    # api, file backend, and registry
  - openstack::keystone  # sets up endpoints
  - openstack::db::mysql # sets up db config
