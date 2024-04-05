.. highlight:: console

.. author:: Lambda <lambda.wtf>

.. categorize your guide! refer to the current list of tags: https://lab.uberspace.de/tags
.. tag:: version-control

.. sidebar:: About

  .. image:: _static/images/softserve.png
      :align: center

##########
Soft Serve
##########

.. tag_list::

`Soft Serve`_ is a self-hosted Git server with a text-based user interface over SSH.

----

.. note:: For this guide you should be familiar with the basic concepts of

  * :manual:`PostgreSQL <database-postgresql>`
  * :manual:`supervisord <daemons-supervisord>`
  * :manual:`domains <web-domains>`

License
=======

`Soft Serve`_ is released under the `MIT License`_.

Prerequisites
=============

Directory
---------

Create a directory for `Soft Serve`_:

.. code-block:: console

 [isabell@stardust ~]$ mkdir -p ~/var/local/lib/soft-serve
 [isabell@stardust ~]$

Firewall
--------

To enable external ssh access to `Soft Serve`_ we will have to open a port in the firewall.

.. include:: includes/open-port.rst

Make sure to note the port number assigned to you for later.

Database
--------

.. note:: You can skip the rest of this step and use a SQLite database instead of the PostgreSQL database. However, this is not recommended for anything except testing usage.

First setup PostgreSQL as specified :lab:`here <guide_postgresql>`. Then create a new user for `Soft Serve`_ and set a password:

.. code-block:: console

 [isabell@stardust ~]$ createuser soft_serve -P
 Enter password for new role:
 Enter it again:
 [isabell@stardust ~]$

After that, create a database owned by the newly created user.

.. code-block:: console

 [isabell@stardust ~]$ createdb --encoding=UTF8 --owner=soft_serve --template=template0 soft_serve
 [isabell@stardust ~]$

Installation
============
To install `Soft Serve`_ we will be using the ``go install`` method. This will install the binary to ``~/go/bin`` which should already be on your ``$PATH``.

.. code-block:: console

 [isabell@stardust ~]$ go install github.com/charmbracelet/soft-serve/cmd/soft@latest
 go: downloading github.com/charmbracelet/soft-serve v0.7.4
 go: downloading github.com/charmbracelet/log v0.3.1
 ...
 go: downloading github.com/gorilla/css v1.0.0
 go: downloading github.com/golang/protobuf v1.5.3
 [isabell@stardust ~]$

Configuration
=============

Create the file ``~/var/local/lib/soft-serve/config.yaml`` and paste the following:

.. code-block:: yaml

 # Soft Serve Server configurations

 # The name of the server.
 # This is the name that will be displayed in the UI.
 name: "Soft Serve"

 # Logging configuration.
 log:
   # Log format to use. Valid values are "json", "logfmt", and "text".
   format: "text"
   # Time format for the log "timestamp" field.
   # Should be described in Golang's time format.
   time_format: "2006-01-02 15:04:05"
   # Path to the log file. Leave empty to write to stderr.
   #path: ""

 # The SSH server configuration.
 ssh:
   # The address on which the SSH server will listen.
   listen_addr: ":<your port>"

   # The public URL of the SSH server.
   # This is the address that will be used to clone repositories.
   public_url: "ssh://<your url>:<your port>"

   # The path to the SSH server's private key.
   key_path: ssh/soft_serve_host_ed25519

   # The path to the server's client private key. This key will be used to
   # authenticate the server to make git requests to ssh remotes.
   client_key_path: ssh/soft_serve_client_ed25519

   # The maximum number of seconds a connection can take.
   # A value of 0 means no timeout.
   max_timeout: 0

   # The number of seconds a connection can be idle before it is closed.
   # A value of 0 means no timeout.
   idle_timeout: 600

 # The Git daemon configuration.
 git:
   # The address on which the Git daemon will listen.
   listen_addr: ":9418"

   # The public URL of the Git daemon server.
   # This is the address that will be used to clone repositories.
   public_url: "git://localhost"

   # The maximum number of seconds a connection can take.
   # A value of 0 means no timeout.
   max_timeout: 0

   # The number of seconds a connection can be idle before it is closed.
   idle_timeout: 3

   # The maximum number of concurrent connections.
   max_connections: 32

 # The HTTP server configuration.
 http:
   # The address on which the HTTP server will listen.
   listen_addr: ":23232"

   # The path to the TLS private key.
   tls_key_path:

   # The path to the TLS certificate.
   tls_cert_path:

   # The public URL of the HTTP server.
   # This is the address that will be used to clone repositories.
   # Make sure to use https:// if you are using TLS.
   public_url: "http://<your url>"

 # The stats server configuration.
 stats:
   # The address on which the stats server will listen.
   listen_addr: "localhost:23233"

 # The database configuration.
 db:
   # The database driver to use.
   # Valid values are "sqlite" and "postgres".
   driver: "postgres"
   # The database data source name.
   # This is driver specific and can be a file path or connection string.
   # Make sure foreign key support is enabled when using SQLite.
   #data_source: "soft-serve.db?_pragma=busy_timeout(5000)&_pragma=foreign_keys(1)"
   data_source: "postgres://soft_serve:<your db password>@localhost:5432/soft_serve?sslmode=disable"

 # Git LFS configuration.
 lfs:
   # Enable Git LFS.
   enabled: true
   # Enable Git SSH transfer.
   ssh_enabled: false

 # Cron job configuration
 jobs:
   mirror_pull: "@every 10m"

 # Additional admin keys.
 #initial_admin_keys:
 #  - "ssh-rsa AAAAB3NzaC1yc2..."


Finishing installation
======================

Point your browser to URL and create a user account.



Updates
=======

.. note:: Check the update feed_ regularly to stay informed about the newest version.


.. _Soft Serve: https://github.com/charmbracelet/soft-serve
.. _MIT License: https://github.com/charmbracelet/soft-serve/blob/main/LICENSE
.. _feed: https://github.com/lorem/ipsum/releases.atom

Debugging
=========

If something fails with this specific error, you should have a look at this specific config, or just reload that service. Try to look into the log at this path.

Backup
======

All generated data you should backup regularly is saved to the database and there is this specific folder with uploaded pictures.

----

Tested with `Soft Serve`_ 0.7.4, Uberspace 7.15.13

.. author_list::
