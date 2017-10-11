Installation
============

<TODO>

MASS Server
-------------

Installation on Ubuntu 17.04
++++++++++++++++++++++++++++

1. Install Ubuntu 17.04
2. Upgrade

   .. code-block:: bash

      apt update
      apt upgrade

3. Follow install instruction on https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/ to install MongoDB. MongoDB does not provide support for non-LTS version. Follow instructions for Ubuntu 16.04 LTS.
4. Install prerequisites

   .. code-block:: bash

      apt install python3-dev build-essential virtualenv

5. Follow install instruction on http://python-ssdeep.readthedocs.io/en/latest/installation.html#install-on-ubuntu-16-04 to install SSDeep.
6. Start MongoDB

   .. code-block:: bash

      service mongod start

7. Clone MASS project and build virtual environment

   .. code-block:: bash

      git clone https://github.com:mass-project/mass_server.git
      cd mass_server
      ./make_venv.sh

8. Activate virtual environment and run MASS

   .. code-block:: bash

      source venv_mass/bin/activate
      python mass_server_flask.py

MASS is know running on 127.0.0.1:5000. Access MASS with your browser. Create new users with the script mass_flask_bin/create_user.py.
