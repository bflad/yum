yum Cookbook
============

The Yum cookbook exposes the `yum_repository` resource that allows a
user to make Yum repositories available for use on a system. The
`yum_repository` resource aims to allow the user to configure all
options listed in the `yum.conf` man page, found at
http://linux.die.net/man/5/yum.conf

NOTES
-----
WARNING: Yum cookbook version 3.0.0 and above contain non-backwards
compatible breaking changes and will not work with cookbooks written
against the 2.x and 1.x series. Changes have been made to the
yum_repository resource, and the yum_key resource has been eliminated
entirely. Recipes have been eliminated and moved into their own
cookbooks. Please lock yum to the 2.x series in your Chef environments
until all dependent cookbooks have been ported.

Requirements
------------
* Chef 11 or higher
* RHEL5, RHEL6, or other platforms within the family

Resources/Providers
-------------------

### yum_repository
This resource manages a yum repository configuration file at
/etc/yum.repos.d/`repositoryid`.repo. When the file needs to be
repaired, it calls yum-makecache so packages in the repo become
available to the next resource.

#### Actions
- :create - creates a repository file and builds the repository listing
- :remove - removes the repository file

#### Parameters
* baseurl -  Must be a URL to the directory where the yum repository's
  'repodata' directory lives. Can be an http://, ftp:// or file://
  URL. You can specify multiple URLs in one baseurl statement.
* cost - relative cost of accessing this repository. Useful for
  weighing one repo's packages as greater/less than any other.
  defaults to 1000
* description - Maps to the 'name' parameter in a repository .conf.
  Descriptive name for the repository channel. This directive must be
  specified.
* enabled - Either `true` or `false`. This tells yum whether or not use this repository.
* enablegroups -  Either `true` or `false`. Determines whether yum
  will allow the use of package groups for this repository. Default is
  `true` (package groups are allowed).
* exclude - List of packages to exclude from updates or installs. This
  should be a space separated list in a single string. Shell globs using wildcards (eg. *
  and ?) are allowed.
* failovermethod - Either 'roundrobin' or 'priority'.
* fastestmirror_enabled - Either `true` or `false`
* gpgcheck - Either `true` or `false`. This tells yum whether or not
  it should perform a GPG signature check on packages. When this is
  set in the [main] section it sets the default for all repositories.
  The default is `true`.
* gpgkey - A URL pointing to the ASCII-armored GPG key file for the
  repository. This option is used if yum needs a public key to verify
  a package and the required key hasn't been imported into the RPM
  database. If this option is set, yum will automatically import the
  key from the specified URL.
* http_caching - Either 'all', 'packages', or 'none'. Determines how
  upstream HTTP caches are instructed to handle any HTTP downloads
  that Yum does. Defaults to 'all'
* includepkgs -  Inverse of exclude. This is a list of packages you
  want to use from a repository. If this option lists only one package
  then that is all yum will ever see from the repository. Defaults to
  an empty list.
* keepalive - Either `true` or `false`. This tells yum whether or not
  HTTP/1.1 keepalive should be used with this repository.  
* max_retries - Set the number of times any attempt to retrieve a file
  should retry before returning an error. Setting this to '0' makes
  yum try forever. Default is '10'.
* metadata_expire - Time (in seconds) after which the metadata will
  expire. So that if the current metadata downloaded is less than this
  many seconds old then yum will not update the metadata against the
  repository. If you find that yum is not downloading information on
  updates as often as you would like lower the value of this option.
  You can also change from the default of using seconds to using days,
  hours or minutes by appending a d, h or m respectively. The default
  is 6 hours, to compliment yum-updatesd running once an hour. It's
  also possible to use the word "never", meaning that the metadata
  will never expire. Note that when using a metalink file the metalink
  must always be newer than the metadata for the repository, due to
  the validation, so this timeout also applies to the metalink file.
* mirrorlist - Specifies a URL to a file containing a list of
  baseurls. This can be used instead of or with the baseurl option.
  Substitution variables, described below, can be used with this
  option. As a special hack is the mirrorlist URL contains the word
  "metalink" then the value of mirrorlist is copied to metalink (if
  metalink is not set)
* mirror_expire - Time (in seconds) after which the mirrorlist locally
  cached will expire. If the current mirrorlist is less than this many
  seconds old then yum will not download another copy of the
  mirrorlist, it has the same extra format as metadata_expire. If you
  find that yum is not downloading the mirrorlists as often as you
  would like lower the value of this option.
* mirrorlist_expire - alias for mirror_expire
* priority - When the yum-priorities plug-in is enabled, you set
  priorities on repository entries, where N is an integer from 1 to 99. The
  default priority for repositories is 99.
* proxy - URL to the proxy server that yum should use.
* proxy_username -  username to use for proxy
* proxy_password - password for this proxy
* report_instanceid - Report instance ID when using Amazon Linux AMIs
  and repositories
* repositoryid - Must be a unique name for each repository, one word.
  Defaults to name attribute.
* sslcacert - Path to the directory containing the databases of the
  certificate authorities yum should use to verify SSL certificates.
  Defaults to none - uses system default
* sslclientcert - Path to the SSL client certificate yum should use to
  connect to repos/remote sites Defaults to none.  
* sslclientkey - Path to the SSL client key yum should use to connect
  to repos/remote sites Defaults to none.
* sslverify - Either `true` or `false`. Determines if yum will verify SSL certificates/hosts. Defaults to `true`
* timeout - Number of seconds to wait for a connection before timing
  out. Defaults to 30 seconds. This may be too short of a time for
  extremely overloaded sites.

#### Example
``` ruby
# add the Zenoss repository
yum_repository 'zenoss' do
  description "Zenoss Stable repo"
  baseurl "http://dev.zenoss.com/yum/stable/"
  gpgkey 'http://dev.zenoss.com/yum/RPM-GPG-KEY-zenoss'
  action :create
end

# add the EPEL repo
yum_repository 'epel' do
  description 'Extra Packages for Enterprise Linux'
  mirrorlist 'http://mirrors.fedoraproject.org/mirrorlist?repo=epel-6&arch=$basearch'
  gpgkey 'http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-6'
  action :create
end
```

``` ruby
# delete CentOS-Media repo
yum_repository 'CentOS-Media' do
  action :delete
end
```

Recipes
-------
All recipes have been removed. Yum is now a "pure" library cookbook,
responsible for the yum_repository resource type.

Related Cookbooks
-----------------
Recipes from older versions of this cookbook have been moved
individual cookbooks. Recipes for managing platform yum configurations
and installing specific repositories can be found in one (or more!) of
the following cookbook.

* yum-centos
* yum-fedora
* yum-amazon
* yum-epel
* yum-elrepo
* yum-repoforge
* yum-ius

Usage
-----
Put `depends 'yum'` in your metadata.rb to gain access to the
yum_repository resource.

License & Authors
-----------------
- Author:: Eric G. Wolfe
- Author:: Matt Ray (<matt@opscode.com>)
- Author:: Joshua Timberman (<joshua@opscode.com>)
- Author:: Sean OMeara (<someara@opscode.com>)

```text
Copyright:: 2011 Eric G. Wolfe
Copyright:: 2011-2013 Opscode, Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
