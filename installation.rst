Installation
============

This guide describes the installation of the MASS Server on a Linux system. Since MASS is a Python application, it should be able to run on almost any Linux system. Though our reference/test platforms are Ubuntu Linux and Alpine Linux. Thus the instructions given below will mainly apply to these distributions. Nonetheless, if you are using a different Linux distribution, you should hopefully be able to adopt the instructions for your system.

Setup a MongoDB server
----------------------

MASS heavily relies on `MongoDB <https://www.mongodb.com/what-is-mongodb>`_ for data storage. Thus, if you not already have a MongoDB server running, you may want to install it now. Usually, you can use your distribution's package manager to install the MongoDB server, e.g. ``apt-get install mongodb-server`` on Ubuntu or ``apk add mongodb`` on Alpine. Please be aware that some distributions may contain a fairly old version of MongoDB, so if you run into any trouble please check if a newer stable version is available.

For the configuration of MongoDB there are a lot of great tutorials on the web, so we will not go into detail about this. However, please be aware that the default MongoDB configuration typically contains **no authentication and access control**, meaning your data will be available to **anyone who can access the server**. For a local development setup this may be acceptable, but make sure to change this for production use!

Install dependencies
--------------------

The Python environment used by the MASS server will need some dependencies to function correctly. These dependencies can be installed with your distribution's package manager:

Ubuntu
^^^^^^

.. code-block:: bash

    apt-get install libffi-dev automake autoconf libtool python3 python3-dev python3-pip git

Alpine
^^^^^^

.. code-block:: bash

    apk add python3 python3-dev libffi libffi-dev libmagic tzdata git build-base automake autoconf libtool linux-headers

Please note that MASS is developed and tested with **Python 3**. It *may* also run with Python 2, but there will be no support and we will not fix any bugs which only occur when using Python 2.

Install the MASS server
-----------------------

General remarks
^^^^^^^^^^^^^^^

MASS uses the `Python ssdeep module <https://pypi.python.org/pypi/ssdeep>`_ to create fuzzy hashes of submitted files. This module requires the ssdeep library to be installed or can alternatively use an included version of this library. Since ssdeep is not included in some Linux distributions, we generally recommend to use the included version. To do this, you have to run the following command before you install the MASS server and its Python dependencies:

.. code-block:: bash

    export BUILD_LIB=1

(Optional) Create a virtual environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Python packages can either be installed system-wide or in a virtual environment. A virtual environment is an isolated Python runtime which allows to install Python packages without interfering with other Python applications on your system. More information about virtual environments can be found `here <https://docs.python.org/3/tutorial/venv.html>`_.

We recommend to use a virtual environment especially for development installations. We should do this now:

.. code-block:: bash

    virtualenv -p /usr/bin/python3 ~/.venv_mass

Then we can activate the new virtual environment:

.. code-block:: bash

    source ~/.venv_mass/bin/activate

The name of the virtual environment should now show up in your bash prompt.

Install for production use
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you just want to run the MASS server and have no intent of working with the source code, you can just issue the following command:

.. code-block:: bash

    pip install mass-server

This will install the stable version of the MASS server from PyPI. Depending on the current development progress, this version may not be the newest available version. If you want to install the most recent version, you can instead use the following command:

.. code-block:: bash

    pip install git+https://github.com/mass-project/mass_server.git

This will install the MASS server from the Github ``master`` branch. This branch should generally be safe to install, but may contain experimental/incomplete features.

Install for development
^^^^^^^^^^^^^^^^^^^^^^^

If you want to work with the MASS server source code, please proceed as follows:

.. code-block:: bash

    cd /some/directory
    git clone https://github.com/mass-project/mass_server.git
    cd mass_server
    pip install -e .

This will install the MASS server in *editable mode* in a directory of your choice. You can then open the source code in your favorite editor/IDE and start hacking :-)

Install the MASS API client
---------------------------

The MASS API client provides a comfortable way to interact with the MASS server using the MASS API. It can be used to connect external systems to MASS or to build a completely new analysis system. For examples, please read the :doc:`/python_client_tutorial`.

The MASS API client can be installed independently of the MASS server and is not required to run the server. Similar to the explanations above, you may install the MASS API client system-wide or in a virtual environment.

Install for production use
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you just want to use the MASS API client to run/develop your own tools and have no intent of working with the MASS API client source code itself, you can just issue the following command:

.. code-block:: bash

    pip install mass-api-client

This will install the stable version of the MASS API client from PyPI. Depending on the current development progress, this version may not be the newest available version. If you want to install the most recent version, you can instead use the following command:

.. code-block:: bash

    pip install git+https://github.com/mass-project/mass_api_client.git

This will install the MASS API client from the Github ``master`` branch. This branch should generally be safe to install, but may contain experimental/incomplete features.

Install for development
^^^^^^^^^^^^^^^^^^^^^^^

If you want to work with the MASS API client source code, please proceed as follows:

.. code-block:: bash

    cd /some/directory
    git clone https://github.com/mass-project/mass_api_client.git
    cd mass_api_client
    pip install -e .

This will install the MASS API client in *editable mode* in a directory of your choice. You can then open the source code in your favorite editor/IDE and start hacking :-)
