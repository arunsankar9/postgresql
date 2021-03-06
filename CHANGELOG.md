# postgresql Cookbook CHANGELOG

This file is used to list changes made in each version of the postgresql cookbook.

_Breaking Change_ Please see UPGRADING.md and the README.md for information how to use.

## v7.0.0 (25-05-2018)

- Add custom resources for:

  - `postgresql_client_install`
  - `postgresql_server_install`
  - `postgresql_repository`
  - `postgresql_pg_gem`

- Deprecate recipes:

  - `apt_pgdg_postgresql`
  - `config_initdb`
  - `config_pgtune`
  - `contrib`
  - `ruby`
  - `yum_pgdg_postgresql`

- Remove deprecated tests

## v6.1.3 (2018-04-18)

- Fix recipes referencing the old helpers

## v6.1.2 (2018-04-16)

**this will be the last release of the 6.0 series before all recipes are removed from the cookbook**

- Deprecate all recipes

## v6.1.1 (2017-03-08)

- Fix pg gem installation on non-omnibus chef runs
- Resolve resource cloning deprecation warnings in the ruby recipe
- Fix issues resolving the timezone on CentOS 7 and probably other distros
- Test with Delivery local instead of Rake

## v6.1.0 (2017-02-18)

- Fix a method name conflict that caused errors if Chef Sugar was also being used on the run list
- Revert a previous PR that added support for Postgresql 9.6 as it introduced incorrect configuration values
- Added Fedora 25 support for pgdg packages
- Added RHEL 5 support for Postgresql 9.4 pgdg packages
- Removed testing for RHEL 5 and Ubuntu 12.04 as they are scheduled for EoL in the near future
- Improvements to Test Kitchen testing to allow more extensive testing in Travis CI
- Fixed the client recipe on Fedora
- Added Inspec tests for client installs

## v6.0.1 (2017-01-04

- Fix systemd unit file template

## v6.0.0 (2017-01-03)

- This cookbook now requires Chef 12.1 or later
- Removed the dependency on the apt cookbook as this functionality is built into modern chef client releases
- Added a new custom resource for installing extensions. This acts as a replacement for the contrib recipe with minimal backwards compatibility. You can now install / remove extensions into any database. This adds the compat_resource cookbook dependency so we can continue to support Chef 12.1-12.4, which lack custom resource support.
- The unused get_result_orig helper has been removed. If you utilized this you'll want to move it to your own wrapper cookbook
- Updates for compatibility with Postgresql 9.5 and 9.6
- Fixed client package installation on openSUSE Leap 42.2
- ca-certificates recipe has been deprecated. If ca-certificates package needs to be upgraded the user should do so prior to including this recipe. Package upgrades in community cookbooks are generally a bad idea as this bring in updated packages to production systems. The recipe currently warns if used and will be removed with the next major cookbook release.
- Fixed RHEL platform detection in the Ruby recipe
- systemd fixes for RHEL systems
- Fix systemd service file include when using pgdg packages
- Package installation now uses multi-package installs to speed up converge times
- Added integration testing in Travis of the client recipe using a new test cookbook. This will be expanded in the future to cover server installation as well
- Expanded the specs to test converges on multiple platforms

## v5.2.0 (2016-12-30)

- Updated contacts and links to point to Sous Chefs now
- Added a Code of Conduct (the Chef CoC)
- Removed duplicate platforms in the metadata
- Fix Chef runs with local mode in the server recipe
- Fix the ruby recipe to not fail when you specify enabling both the apt and yum repos for mixed distro environments
- Set the postgresql data directory to 700 permissions
- Added node['postgresql']['pg_gem']['version'] to specify the version of the pg gem to install
- Cookstyle fixes for the latest cookstyle release
- Removed test deps from the Gemfile. Rely on ChefDK for base testing deps instead

## v5.1.0 (2016-11-01)

- Maintenance of this cookbook has been migrated from Heavy Water to Sous Chefs - <https://sous-chefs.org/>
- Add support for Chef-Zero (local mode)
- Don't hardcode the UID / GID on RHEL/Amazon/Suse platforms
- Add PGDG yum RPMs for 9.5 / 9.6

## v5.0.0 (2016-10-25)

### Breaking changes

- Switched from Librarian to Berkshelf
- Remove support for the following platforms

  - SLES < 12
  - openSUSE < 13
  - Debian < 7
  - Ubuntu < 12.04
  - RHEL < 6
  - Amazon < 2013
  - Unsupported (EOL) Fedora releases

### Other changes

- Added support for Ubuntu 16.04
- Loosened cookbook dependencies to not prevent pulling in the latest community cookbooks
- Added chef_version metadata
- Switched from rubocop to cookstyle and fix all warnings
- Removed minitests and the minitest handler
- Added support for opensuse / opensuseleap
- Added support for Fedora 23/24
- Added a chefignore file to limit the files uploaded to the chef server
- Updated Test Kitchen config to test on modern platform releases
- Added a Rakefile and updated Travis to test with ChefDK and that rakefile
- Avoid installing packages included in build-essential twice in the ruby recipe
- Require at least build-essential 2.0
- Don't cleanup the old PPA files in the apt_pgdg_postgresql recipe anymore. These should be long gone everywhere
- Remove logic in the apt_pgdg_postgresql recipe that made Chef fail when new distro releases came out
- Avoid node.set deprecation warnings
- Avoid managed_home deprecation warnings in server_redhat recipe

## v4.0.6

- Add 16.04 Xenial to the allowed list

## v4.0.4

- Add leading pound symbol on pg_hba.conf template comment line
- Update gem install for compile_time to correct deprication warning
- Add support Ubuntu Wily Werewolf pgdg apt repository
- test-kitchen platforms for Centos 7.2 and Ubuntu 15.04
- Fixes PostgreSQL version & package name defaults for EL7 distros
- Add appropriate systemd unit file overrides for EL7 distros

## v4.0.2

- Add Code of Conduct
- Add Rubocop
- Clean up of syntax in many places as result of adding and evaluating Rubocop
- Updates to test-kitchen.yml
- added additional attribute for people who are importing pgdg packages for internal repositories

  - `default['postgresql']['use_pgdg_packages'] = false`

## v4.0.0

**WARNING: Please read carefully through the stated changes, as they probably will break your current setup and can result in duplicate postgresql versions being installed, configuration corruption and data loss! This list might not be complete, so be careful when using the 4.x version and make sure to test it extensively before production use!**

When in doubt, put the following in your `Berksfile` until you are ready to upgrade:

```ruby
cookbook 'postgresql', '~> 3.4.0'
```

- Potential breaking change: Restructured default attributes to avoid compile time deriving other attribute values from value of the `node[‘postgresql’][‘version’]` (#313, #302, #295, #288, #280, #261, #260, #254, #248, #217, #214, #167, #143). If you specify a custom postgresql version, make sure to adapt the following attributes as well:

```ruby
default['postgresql']['dir'] = "/etc/postgresql/#{node['postgresql']['version']}/main"
default['postgresql']['client']['packages'] = [ "postgresql-client-#{node['postgresql']['version']}", 'libpq-dev' ]
default['postgresql']['server']['packages'] = [ "postgresql-#{node['postgresql']['version']}" ]
default['postgresql']['contrib']['packages'] = [ "postgresql-contrib-#{node['postgresql']['version']}" ]
```

- Potential breaking change: SSL configuration parameters. Due to the new structuring, make sure you set all SSL attributes to `override` when specifying them in a cookbook:

```ruby
override['postgresql']['config']['ssl'] = true
override['postgresql']['config']['ssl_cert_file'] = "/path/to/cert.crt"
override['postgresql']['config']['ssl_key_file']  = "/path/to/cert.key"
override['postgresql']['config']['ssl_ciphers']  = "<my cipher suite>"
```

- Potential breaking change: Some node attributes are now persistet in your node configuration. This affects the following attributes:

```json
"config": {
  "data_directory": "/var/lib/postgresql/9.4/main",
  "hba_file": "/etc/postgresql/9.4/main/pg_hba.conf",
  "ident_file": "/etc/postgresql/9.4/main/pg_ident.conf",
  "external_pid_file": "/var/run/postgresql/9.4-main.pid",
  "unix_socket_directories": "/var/run/postgresql",
  "ssl_cert_file": "/etc/ssl/certs/ssl-cert-snakeoil.pem",
  "ssl_key_file": "/etc/ssl/private/ssl-cert-snakeoil.key"
}
```

- Potential breaking change: Parsing of attributes from node/ environment configuration. It has been reported that setting the `node['postgresql']['client']['packages']` attribute in a cookbook might result in the default version of the postgresql client package being installed alongside the required version. This might affect the server packages as well.
- Correct issues which caused the inability to override installation version defaults
- Correct issues which caused configuration file entries with miss matching version numbers and incorrect file system paths being defined
- Remove method pgdgrepo_rpm_info compile time use of derived attributes case many issues
- Use correct directory path and check for the correct not_if condition to determine if the database has been initialized
- Ensure that correct packages are installed in all scenarios where pg gem is compiled
- Fix errors in configuration files for unix_socket_directory and unix_socket_directories
- Updates to test-kitchen suite configuration
- Added more grey hair to my beard

## v3.4.24

- Corrections to address repositories signed with newer certificates that some distributions have in their default ca-certificates package
- Updates to more accurately determine distributions service init systems adds better support for systemd systems
- Correct how version attribute is evaluated in certain places
- test-kitchen suite configuration corrections
- Opensuse support

## v3.4.23

- Skipping 3.4.22 with Develop branch 3.4.23 to return to releasing cookbook from master on even numbers and develop on odd numbers.

## v3.4.21

- Use more optimistic openssl version constraint
- Add Postgresql 9.4 package sources for RHEL platforms
- Update testing infrastructure to address bit rot

## v3.4.20

- Revert [#251](https://github.com/sous-chefs/postgresql/pull/251), a change which caused the postgresql service to restart every Chef run.

## v3.4.19

- node.save could better not be run on every chef run since it causes node.default attributes stored to the node objects to differ during a chef run and when
- Missing attribute in docs for yum_pgdg_postgresql
- restart postgres service immediately on config change
- Run restart command right away on the postgresql service.
- Add kitchen test for shared_preload_libraries & extension setup.
- Fix install order of contrib packages to fix pg_stat_statements issues.
- Add Debian Jessie to whitelist for apt.postgresql.org repo
- Install version 9.4 on Debian Jessie
- add amazon 2015
- add rhel7 support

## v3.4.18

- Revert changes from #201 with the intention of revisiting these changes as part of the next major version release.
- Specify version constraint on openssl cookbook due to an upstream release mishap

## v3.4.16

- Changed hard coded value to attribute #219
- Correction for directory creation under debian, etc. #222
- Fedora 20 yum support #223
- Define version-sensitive attributes in a recipe #201

## v3.4.14

- Support apt repository for Ubuntu Utopic 14.10
- Do not try and set password on standby hosts

## v3.4.12

- Create configuration templates at the appropriate time
- If template is updated restart service changed to default of :delayed
- Fix SSL for PostgreSQL versions < 9.2

## v3.4.10

- correct conditional error created in 3.4.8.

## v3.4.8

- Correct scenario where work_mem could be set to 0 if con is greater than mem Issue #185
- Add Centos7 suites to kitchen configuration

## v3.4.6

- Don't include the pgdg recipes on the wrong machine types
- Add missing dir /etc/sysconfig/pgsl for centos7
- CentOS 7 package support

## v3.4.4

- fix packages on SLES11SP2 and higher
- [COOK-4737] Add flag to control database user password behavior
- add amazon platform rpm info
- Fix issues with the server_redhat recipe on Fedora 16 and later
- attribute typo correction
- correctly check and set max_connections to an integer

## v3.4.2

- Changed the Gem::Installer::ExtensionBuildError to a Mixlib::ShellOut::ShellCommandFailed

## v3.4.1

- Added support for Ubuntu 14.04 and Postgresql 9.3
- Fix [COOK-3490] <https://tickets.opscode.com/browse/COOK-3490>

## v3.4.0

Updated CONTRIBUTING document. Refreshed test kitchen configuration. Merged Pull Requests: 122, 116, 104, 102, 99, 96, 93, 90.

## v3.3.4

Testing

## v3.3.2

- Testing maintainer transfer to Heavywater with Opscode as collaborator

## v3.3.0

### Bug

- **[COOK-3851](https://tickets.opscode.com/browse/COOK-3851)** - Postgresql: reload after config change does not pick up certain configuration changes
- **[COOK-3611](https://tickets.opscode.com/browse/COOK-3611)** - unix_socket_directory does not exists in 9.3
- **[COOK-2954](https://tickets.opscode.com/browse/COOK-2954)** - PostgreSQL installation ignores version attribute on CentOS >= 6

## v3.2.0

- [COOK-3717] Pgdg repositories improvements
- [COOK-3756] Change postgresql.conf mode from 0600 to 0644

## v3.1.0

### Improvement

- **[COOK-3685](https://tickets.opscode.com/browse/COOK-3685)** - Upgrade Repo Attributes for Postgresql 9.3
- **[COOK-3597](https://tickets.opscode.com/browse/COOK-3597)** - Fix implementation of `initdb_locale` attribute for RHEL
- **[COOK-3566](https://tickets.opscode.com/browse/COOK-3566)** - Give the user's rules more priority than the default ones in pg_hba
- **[COOK-3553](https://tickets.opscode.com/browse/COOK-3553)** - Remove automatic `apt-get update`

### Bug

- **[COOK-3611](https://tickets.opscode.com/browse/COOK-3611)** - Remove `unix_socket_directory` (it does not exists in 9.3)
- **[COOK-3599](https://tickets.opscode.com/browse/COOK-3599)** - Automatically add PGDG apt repo dependency on PostgreSQL version
- **[COOK-3555](https://tickets.opscode.com/browse/COOK-3555)** - Documentation Fix
- **[COOK-2383](https://tickets.opscode.com/browse/COOK-2383)** - Update Postgres version in attributes

## v3.0.4

### Bug

- **[COOK-3173](https://tickets.opscode.com/browse/COOK-3173)** - Use :reload instead of :restart on conf changes
- **[COOK-2939](https://tickets.opscode.com/browse/COOK-2939)** - Fix RedHat support

## v3.0.2

### Bug

- [COOK-3076]: postgresql::ruby recipe error when using pgdg repositories

## v3.0.0

This is a backwards-incompatible release because the Pitti PPA is deprecated and the recipe removed, replaced with the PGDG apt repository.

### Bug

- [COOK-2571]: Create helper library for pg extension detection
- [COOK-2797]: Contrib extension contianing '-' fails to load.

### Improvement

- [COOK-2387]: Pitti Postgresql PPA is deprecated

### Task

- [COOK-3022]: update baseboxes in .kitchen.yml

## v2.4.0

- [COOK-2163] - Dangerous "assign-postgres-password" in "recipes/server.rb" -- Can lock out dbadmin access
- [COOK-2390] - Recipes to auto-generate many postgresql.conf settings, following "initdb" and "pgtune"
- [COOK-2435] - Foodcritic fixes for postgresql cookbook
- [COOK-2476] - Installation into database of any contrib module extensions listed in a node attribute

## v2.2.2

- [COOK-2232] -Provide PGDG yum repo to install postgresql 9.x on redhat-derived distributions

## v2.2.0

- [COOK-2230] - Careful about Debian minor version numbers
- [COOK-2231] - Fix support for postgresql 9.x in server_redhat recipe
- [COOK-2238] - Postgresql recipe error in password check
- [COOK-2176] - PostgreSQL cookbook in Solo mode can cause "NoMethodError: undefined method `[]' for nil:NilClass"
- [COOK-2233] - Provide postgresql::contrib recipe to install useful server administration tools

## v2.1.0

- [COOK-1872] - Allow latest PostgreSQL deb packages to be installed
- [COOK-1961] - Postgresql config file changes with every Chef run
- [COOK-2041] - Postgres cookbook no longer installs on OpenSuSE 11.4

## v2.0.2

- [COOK-1406] - pg gem compile is unable to find libpq under Chef full stack (omnibus) installation

## v2.0.0

This version is backwards incompatible with previous versions of the cookbook due to use of `platform_family`, and the refactored configuration files using node attributes. See README.md for details on how to modify configuration of PostgreSQL.

- [COOK-1508] - fix mixlib shellout error on SUSE
- [COOK-1744] - Add service enable & start
- [COOK-1779] - Don't run apt-get update and others in ruby recipe if pg is installed
- [COOK-1871] - Attribute driven configuration files for PostgreSQL
- [COOK-1900] - don't assume ssl on all postgresql 8.4+ installs
- [COOK-1901] - fail a chef-solo run when the postgres password attribute is not set

## v1.0.0

**Important note for this release**

This version no longer installs Ruby bindings in the client recipe by default. Use the ruby recipe if you'd like the RubyGem. If you'd like packages for your distribution, use them in your application's specific cookbook/recipe, or modify the client packages attribute.

This resolves the following tickets.

- COOK-1011
- COOK-1534

The following issues are also resolved with this release.

- [COOK-1011] - Don't install postgresql packages during compile phase and remove pg gem installation
- [COOK-1224] - fix undefined variable on Debian
- [COOK-1462] - Add attribute for specifying listen address

## v0.99.4

- [COOK-421] - config template is malformed
- [COOK-956] - add make package on ubuntu/debian

## v0.99.2

- [COOK-916] - use < (with float) for version comparison.

## v0.99.0

- Better support for Red Hat-family platforms
- Integration with database cookbook
- Make sure the postgres role is updated with a (secure) password
