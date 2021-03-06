.. _administrator_deploy:

Deploy the application
======================

The first tine you want do deploy an application the configuration 
should be setup in the file ``deploy/deploy.cfg[.in]``.

The deploy tool has four parts:

* ``[file]`` to deploy the GeoTIFF, ShapeFiles and so on.
* ``[database]`` to deploy the database.
* ``[code]`` to deploy the application.
* ``[apache]`` to build the apache config.

An other important section is the ``[remote_hosts]`` where we 
configure the demo and production hosts.

All the configuration option can be found in ``/etc/deploy/deploy.cfg``.

The command line help ``deploy --help``::

    Usage: deploy -c [OPTIONS]... CONFIG_FILE DIRECTORY
       or: deploy -x [OPTIONS]... DIRECTORY
       or: deploy -r [OPTIONS] CONFIG_FILE HOST

    Options:
      -h, --help            show this help message and exit
      -v, --verbose         make lots of noise [default]
      -q, --quiet           be vewwy quiet
      -e ENV, --env=ENV     additionals environement variables, eg: '-e
                            target=prod,foo=bar'

      Create an archive:
        -c, --create        create a new archive
        --components=COMPONENTS
                            restrict component to update. [databases,files,code].
                            default to all
        --tables=TABLES     only include TABLES. eg: '--tables foo,bar.baz' to
                            include the database 'foo' and the 'baz' table from
                            the 'bar' database
        --symlink           use symlinks for 'files' and 'code'
        --no-symlink        don't use symlinks for 'files' and 'code' (copy
                            content) [default]

      Extract an archive:
        -x, --extract       extract files from an archive
        -d, --delete        delete the archive after the restoration [default]
        -k, --keep          don't delete the archive after the restoration

      Create, copy and extract:
        -r, --remote        create, copy and restore an archive to a remote server
        --no-time-dir       don't create separated archive directory for each
                            remote deploy [default false]


To deploy from dev to demo
--------------------------

Build on the dev server::

  ssh -A <dev_server> # SSH agent forward is needed
  cd /var/www/<your_vhost>/public/<your_project>
  svn up # update the code
  rm -rf buildout/parts/modwsgi # to prevent rights error
  ./buildout/bin/buildout -c buildout_main.cfg # configure c2cgeoportal

**Test on the dev server**
    
Deploy to the demo server::

  rm -rf buildout/parts/modwsgi # to prevent rights error
  cd deploy
  sudo -u deploy deploy -r deploy.cfg demo 
  ./buildout/bin/buildout -c buildout_main.cfg # to make dev working again

**Test on the demo server**


To deploy from demo to prod
---------------------------

**Test on the demo server**

Deploy on the prod server::

  ssh -A <demo_server> # SSH agent forward is needed
  cd /var/www/<your_vhost>/public/<your_project>
  cd deploy
  sudo -u deploy deploy -r deploy.cfg prod 

**Test on the prod server**
