=====================================================
bash
=====================================================
`[edit on GitHub] <https://github.com/chef/chef-web-docs/blob/master/chef_master/source/resource_bash.rst>`__

.. tag resource_script_bash

Use the **bash** resource to execute scripts using the Bash interpreter. This resource may also use any of the actions and properties that are available to the **execute** resource. Commands that are executed with this resource are (by their nature) not idempotent, as they are typically unique to the environment in which they are run. Use ``not_if`` and ``only_if`` to guard this resource for idempotence.

.. note:: The **bash** script resource (which is based on the **script** resource) is different from the **ruby_block** resource because Ruby code that is run with this resource is created as a temporary file and executed like other script resources, rather than run inline.

.. end_tag

Syntax
=====================================================
A **bash** resource block executes scripts using Bash:

.. code-block:: ruby

   bash 'extract_module' do
     cwd ::File.dirname(src_filepath)
     code <<-EOH
       mkdir -p #{extract_path}
       tar xzf #{src_filename} -C #{extract_path}
       mv #{extract_path}/*/* #{extract_path}/
       EOH
     not_if { ::File.exist?(extract_path) }
   end

where

* ``cwd`` specifies the directory from which the command is run
* ``code`` specifies the command to run

The full syntax for all of the properties that are available to the **bash** resource is:

.. code-block:: ruby

   bash 'name' do
     code                       String
     creates                    String
     cwd                        String
     environment                Hash
     flags                      String
     group                      String, Integer
     notifies                   # see description
     path                       Array
     provider                   Chef::Provider::Script::Bash
     returns                    Integer, Array
     subscribes                 # see description
     timeout                    Integer, Float
     user                       String, Integer
     umask                      String, Integer
     action                     Symbol # defaults to :run if not specified
   end

where

* ``bash`` is the resource
* ``name`` is the name of the resource block
* ``cwd`` is the location from which the command is run
* ``action`` identifies the steps the chef-client will take to bring the node into the desired state
* ``code``, ``creates``, ``cwd``, ``environment``, ``flags``, ``group``, ``path``, ``provider``, ``returns``, ``timeout``, ``user``, and ``umask`` are properties of this resource, with the Ruby type shown. See "Properties" section below for more information about all of the properties that may be used with this resource.

Actions
=====================================================
This resource has the following actions:

``:nothing``
   Prevent a command from running. This action is used to specify that a command is run only when another resource notifies it.

``:run``
   Default. Run a script.

Properties
=====================================================
This resource has the following properties:

``code``
   **Ruby Type:** String

   A quoted (" ") string of code to be executed.

``creates``
   **Ruby Type:** String

   Prevent a command from creating a file when that file already exists.

``cwd``
   **Ruby Type:** String

   The current working directory.

``environment``
   **Ruby Type:** Hash

   A Hash of environment variables in the form of ``({"ENV_VARIABLE" => "VALUE"})``. (These variables must exist for a command to be run successfully.)

``flags``
   **Ruby Type:** String

   One or more command line flags that are passed to the interpreter when a command is invoked.

``group``
   **Ruby Types:** String, Integer

   The group name or group ID that must be changed before running a command.

``ignore_failure``
   **Ruby Types:** TrueClass, FalseClass

   Continue running a recipe if a resource fails for any reason. Default value: ``false``.

``notifies``
   **Ruby Type:** Symbol, 'Chef::Resource[String]'

   .. tag resources_common_notification_notifies

   A resource may notify another resource to take action when its state changes. Specify a ``'resource[name]'``, the ``:action`` that resource should take, and then the ``:timer`` for that action. A resource may notifiy more than one resource; use a ``notifies`` statement for each resource to be notified.

   .. end_tag

   .. tag resources_common_notification_timers

   A timer specifies the point during the chef-client run at which a notification is run. The following timers are available:

   ``:before``
      Specifies that the action on a notified resource should be run before processing the resource block in which the notification is located.

   ``:delayed``
      Default. Specifies that a notification should be queued up, and then executed at the very end of the chef-client run.

   ``:immediate``, ``:immediately``
      Specifies that a notification should be run immediately, per resource notified.

   .. end_tag

   .. tag resources_common_notification_notifies_syntax

   The syntax for ``notifies`` is:

   .. code-block:: ruby

      notifies :action, 'resource[name]', :timer

   .. end_tag

``path``
   **Ruby Type:** Array

   An array of paths to use when searching for a command. These paths are not added to the command's environment $PATH. The default value uses the system path.

   .. warning:: .. tag resources_common_resource_execute_attribute_path

                The ``path`` property is not implemented by any provider in any version of the chef-client. Starting with chef-client 12, using the ``path`` property will return a warning. Starting with chef-client 13, the ``path`` property is deprecated and using it will return an exception. Cookbooks that currently use the ``path`` property should be updated to use the ``environment`` property instead.

                .. end_tag

      For example:

      .. code-block:: ruby

         bash 'mycommand' do
           environment 'PATH' => "/my/path/to/bin:#{ENV['PATH']}"
         end

``provider``
   **Ruby Type:** Chef Class

   Optional. Explicitly specifies a provider. See "Providers" section below for more information.

``retries``
   **Ruby Type:** Integer

   The number of times to catch exceptions and retry the resource. Default value: ``0``.

``retry_delay``
   **Ruby Type:** Integer

   The retry delay (in seconds). Default value: ``2``.

``returns``
   **Ruby Types:** Integer, Array

   The return value for a command. This may be an array of accepted values. An exception is raised when the return value(s) do not match. Default value: ``0``.

``subscribes``
   **Ruby Type:** Symbol, 'Chef::Resource[String]'

   .. tag resources_common_notification_subscribes

   A resource may listen to another resource, and then take action if the state of the resource being listened to changes. Specify a ``'resource[name]'``, the ``:action`` to be taken, and then the ``:timer`` for that action.

   .. end_tag

   .. tag resources_common_notification_timers

   A timer specifies the point during the chef-client run at which a notification is run. The following timers are available:

   ``:before``
      Specifies that the action on a notified resource should be run before processing the resource block in which the notification is located.

   ``:delayed``
      Default. Specifies that a notification should be queued up, and then executed at the very end of the chef-client run.

   ``:immediate``, ``:immediately``
      Specifies that a notification should be run immediately, per resource notified.

   .. end_tag

   .. tag resources_common_notification_subscribes_syntax

   The syntax for ``subscribes`` is:

   .. code-block:: ruby

      subscribes :action, 'resource[name]', :timer

   .. end_tag

``timeout``
   **Ruby Types:** Integer, Float

   The amount of time (in seconds) a command is to wait before timing out. Default value: ``3600``.

``user``
   **Ruby Types:** String, Integer

   The user name or user ID that should be changed before running a command.

``umask``
   **Ruby Types:** String, Integer

   The file mode creation mask, or umask.

Guards
-----------------------------------------------------
.. tag resources_common_guards

A guard property can be used to evaluate the state of a node during the execution phase of the chef-client run. Based on the results of this evaluation, a guard property is then used to tell the chef-client if it should continue executing a resource. A guard property accepts either a string value or a Ruby block value:

* A string is executed as a shell command. If the command returns ``0``, the guard is applied. If the command returns any other value, then the guard property is not applied. String guards in a **powershell_script** run Windows PowerShell commands and may return ``true`` in addition to ``0``.
* A block is executed as Ruby code that must return either ``true`` or ``false``. If the block returns ``true``, the guard property is applied. If the block returns ``false``, the guard property is not applied.

A guard property is useful for ensuring that a resource is idempotent by allowing that resource to test for the desired state as it is being executed, and then if the desired state is present, for the chef-client to do nothing.

.. end_tag

**Attributes**

.. tag resources_common_guards_attributes

The following properties can be used to define a guard that is evaluated during the execution phase of the chef-client run:

``not_if``
   Prevent a resource from executing when the condition returns ``true``.

``only_if``
   Allow a resource to execute only if the condition returns ``true``.

.. end_tag

**Arguments**

.. tag resources_common_guards_arguments

The following arguments can be used with the ``not_if`` or ``only_if`` guard properties:

``:user``
   Specify the user that a command will run as. For example:

   .. code-block:: ruby

      not_if 'grep adam /etc/passwd', :user => 'adam'

``:group``
   Specify the group that a command will run as. For example:

   .. code-block:: ruby

      not_if 'grep adam /etc/passwd', :group => 'adam'

``:environment``
   Specify a Hash of environment variables to be set. For example:

   .. code-block:: ruby

      not_if 'grep adam /etc/passwd', :environment => {
        'HOME' => '/home/adam'
      }

``:cwd``
   Set the current working directory before running a command. For example:

   .. code-block:: ruby

      not_if 'grep adam passwd', :cwd => '/etc'

``:timeout``
   Set a timeout for a command. For example:

   .. code-block:: ruby

      not_if 'sleep 10000', :timeout => 10

.. end_tag

Providers
=====================================================
.. tag resources_common_provider

Where a resource represents a piece of the system (and its desired state), a provider defines the steps that are needed to bring that piece of the system from its current state into the desired state.

.. end_tag

.. tag resources_common_provider_attributes

The chef-client will determine the correct provider based on configuration data collected by Ohai at the start of the chef-client run. This configuration data is then mapped to a platform and an associated list of providers.

Generally, it's best to let the chef-client choose the provider, and this is (by far) the most common approach. However, in some cases, specifying a provider may be desirable. There are two approaches:

* Use a more specific short name---``yum_package "foo" do`` instead of ``package "foo" do``, ``script "foo" do`` instead of ``bash "foo" do``, and so on---when available
* Use the ``provider`` property within the resource block to specify the long name of the provider as a property of a resource. For example: ``provider Chef::Provider::Long::Name``

.. end_tag

This resource has the following providers:

``Chef::Provider::Script``, ``script``
   When this short name is used, the chef-client will determine the correct provider during the chef-client run.

``Chef::Provider::Script::Bash``, ``bash``
   The provider for the Bash command interpreter.

Examples
=====================================================
The following examples demonstrate various approaches for using resources in recipes. If you want to see examples of how Chef uses resources in recipes, take a closer look at the cookbooks that Chef authors and maintains: https://github.com/chef-cookbooks.

**Use a named provider to run a script**

.. tag resource_script_bash_provider_and_interpreter

.. To use the |resource bash| resource to run a script:

.. code-block:: ruby

   bash 'install_something' do
     user 'root'
     cwd '/tmp'
     code <<-EOH
     wget http://www.example.com/tarball.tar.gz
     tar -zxf tarball.tar.gz
     cd tarball
     ./configure
     make
     make install
     EOH
   end

.. end_tag

**Install a file from a remote location using bash**

.. tag resource_remote_file_install_with_bash

The following is an example of how to install the ``foo123`` module for Nginx. This module adds shell-style functionality to an Nginx configuration file and does the following:

* Declares three variables
* Gets the Nginx file from a remote location
* Installs the file using Bash to the path specified by the ``src_filepath`` variable

.. code-block:: ruby

   # the following code sample is similar to the ``upload_progress_module``
   # recipe in the ``nginx`` cookbook:
   # https://github.com/chef-cookbooks/nginx

   src_filename = "foo123-nginx-module-v#{
     node['nginx']['foo123']['version']
   }.tar.gz"
   src_filepath = "#{Chef::Config['file_cache_path']}/#{src_filename}"
   extract_path = "#{
     Chef::Config['file_cache_path']
     }/nginx_foo123_module/#{
     node['nginx']['foo123']['checksum']
   }"

   remote_file 'src_filepath' do
     source node['nginx']['foo123']['url']
     checksum node['nginx']['foo123']['checksum']
     owner 'root'
     group 'root'
     mode '0755'
   end

   bash 'extract_module' do
     cwd ::File.dirname(src_filepath)
     code <<-EOH
       mkdir -p #{extract_path}
       tar xzf #{src_filename} -C #{extract_path}
       mv #{extract_path}/*/* #{extract_path}/
       EOH
     not_if { ::File.exist?(extract_path) }
   end

.. end_tag

**Install an application from git using bash**

.. tag resource_scm_use_bash_and_ruby_build

The following example shows how Bash can be used to install a plug-in for rbenv named ``ruby-build``, which is located in git version source control. First, the application is synchronized, and then Bash changes its working directory to the location in which ``ruby-build`` is located, and then runs a command.

.. code-block:: ruby

   git "#{Chef::Config[:file_cache_path]}/ruby-build" do
     repository 'git://github.com/sstephenson/ruby-build.git'
     reference 'master'
     action :sync
   end

   bash 'install_ruby_build' do
     cwd '#{Chef::Config[:file_cache_path]}/ruby-build'
     user 'rbenv'
     group 'rbenv'
     code <<-EOH
       ./install.sh
       EOH
     environment 'PREFIX' => '/usr/local'
  end

To read more about ``ruby-build``, see here: https://github.com/sstephenson/ruby-build.

.. end_tag

**Store certain settings**

.. tag resource_remote_file_store_certain_settings

The following recipe shows how an attributes file can be used to store certain settings. An attributes file is located in the ``attributes/`` directory in the same cookbook as the recipe which calls the attributes file. In this example, the attributes file specifies certain settings for Python that are then used across all nodes against which this recipe will run.

Python packages have versions, installation directories, URLs, and checksum files. An attributes file that exists to support this type of recipe would include settings like the following:

.. code-block:: ruby

   default['python']['version'] = '2.7.1'

   if python['install_method'] == 'package'
     default['python']['prefix_dir'] = '/usr'
   else
     default['python']['prefix_dir'] = '/usr/local'
   end

   default['python']['url'] = 'http://www.python.org/ftp/python'
   default['python']['checksum'] = '80e387...85fd61'

and then the methods in the recipe may refer to these values. A recipe that is used to install Python will need to do the following:

* Identify each package to be installed (implied in this example, not shown)
* Define variables for the package ``version`` and the ``install_path``
* Get the package from a remote location, but only if the package does not already exist on the target system
* Use the **bash** resource to install the package on the node, but only when the package is not already installed

.. code-block:: ruby

   #  the following code sample comes from the ``oc-nginx`` cookbook on |github|: https://github.com/cookbooks/oc-nginx

   version = node['python']['version']
   install_path = "#{node['python']['prefix_dir']}/lib/python#{version.split(/(^\d+\.\d+)/)[1]}"

   remote_file "#{Chef::Config[:file_cache_path]}/Python-#{version}.tar.bz2" do
     source "#{node['python']['url']}/#{version}/Python-#{version}.tar.bz2"
     checksum node['python']['checksum']
     mode '0755'
     not_if { ::File.exist?(install_path) }
   end

   bash 'build-and-install-python' do
     cwd Chef::Config[:file_cache_path]
     code <<-EOF
       tar -jxvf Python-#{version}.tar.bz2
       (cd Python-#{version} && ./configure #{configure_options})
       (cd Python-#{version} && make && make install)
     EOF
     not_if { ::File.exist?(install_path) }
   end

.. end_tag
