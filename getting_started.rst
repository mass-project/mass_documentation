Getting started
===============

After you have completed the :doc:`/installation` it is now time to get started with using MASS.

Configuration
-------------

Before we can start the server, we need to set some configuration settings. Configuration is done by creating an ``application.cfg`` file in the MASS server instance folder. Depending on the installation method the location of this folder may vary. If you are not sure about the correct location, you can start the server (see below) and it will print an error message with the location where it expects the configuration to be.

A minimal configuration file must at least contain the MongoDB connection settings, for example:

.. code-block:: python

    MONGODB_SETTINGS = {
        'host': 'mongodb://localhost:27017/mass-db',
        'tz_aware': True
    }

Depending on your MongoDB configuration it may be necessary to provide additional arguments, such as a username and password. Have a look at the `Flask-MongoEngine documentation <https://flask-mongoengine.readthedocs.io/en/latest/>`_ for details.

First start
-----------

If everything is installed and configured correctly, you should now be ready to start the MASS server for the first time. In this tutorial we will use the *development server* to do so. It will automatically enable debug mode and print detailed error reports if something goes wrong. We can start the server by running the following command:

.. code-block:: bash

    mass_server run_development

If everything works fine, you should see an output similar to this::

    INFO:werkzeug: * Running on http://127.0.0.1:8000/ (Press CTRL+C to quit)
    INFO:werkzeug: * Restarting with stat
    WARNING:werkzeug: * Debugger is active!
    INFO:werkzeug: * Debugger PIN: 315-394-951

Open up ``http://localhost:8000`` in your web browser and you should see the MASS web interface.

Create a user
-------------

Most functionality of the MASS server is only available to logged in users. Thus we should create an initial admin user now. Thus we run the following command:

.. code-block:: bash

    mass_server create_user
    Please enter username to create: mass-admin
    Please enter the password for the new user: *****
    Enter password again to verify: *****
    Should the new user have admin privileges? [y/n] y

Start the MASS server (if not already running) and try to login with the newly created user.
