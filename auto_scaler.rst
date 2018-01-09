MASS Autoscaler
===============

Introduction
------------
The MASS Autoscaler is a tool for the MASS Server that provides the functionality to automatically create or quit replicas of running Analysis Instances, depending on current demand. This ensures that no unnecessary number of unused instances are executed. In this tutorial we will start a Docker-Service with an analysis System. Then the MASS Autoscaler is set up to connect to the MASS server. From there, he will automatically find the Docker-Service and scale it according to the calculated demand.


Setup of Analysis Systems
-------------------------
Consider the following two aspects before you begin setting up the Analysis System.

	1. For the following steps it is recommended that you can use a Dockerfile to create an image and start a Service based on that image. You can get more information about this topic in the Docker `Documentation. <https://docs.docker.com/>`_

	2. Please make sure that the server is not just listening on localhost. You can do this by using the following line to start the server.

	 .. code-block:: bash

		mass_server run_development --listen 0.0.0.0


First, there should be a working MASS Analysis System. Any Analysis System based on the MASS API Client can be used here. In this tutorial, we will use `mass_system_ssdeep <https://github.com/mass-project/mass_system_ssdeep>`_.

Now you have to configure the Analysis System. In our case, it is sufficient to edit the address of the MASS server and our API key in the *config.ini* file. When this is done you can create an image of this Analysis System. 

The next step is to create a Service based on the created image. You can do this by using the following command in the directory where the Dockerfile is located.

.. code-block:: bash

	docker build -t ssdeep .

Now it is time to create a Service with running Analysis Instances. A Service can be easily created and labeled via a Docker compose file. In the following you will find a sample file for this case. It also shows which form these labels must have.
It is important for the MASS Autoscaler that the current Services have labels that indicate that this Service belongs to a MASS Analysis System. These labels provide information about the Service, such as the Analysis System, the minimum and maximum number of instances, etc. .

.. literalinclude:: compose.txt

Keep in mind that undefined labels are automatically interpreted as default values.


Configuration & Start
---------------------
The next step is to configure the Autoscaler. This is done by configuring the settings and defining the API Key.
To configure the Autoscaler you have to adjust the entries in the *config.py* file. At the beginning it should be enough to define the server address and die API Key. Explanations of the individual options can be found in the configuration file.

Change to the directory of the Autoscaler and start it with the following input in the command line. 


.. code-block:: bash

	python autoscaler.py

Now the autoscaler monitors the running Services and adjusts the replica number based on the number of Analysis Requests.

During runtime, additional Services can be started and existing Services can be terminated at any time. The Autoscaler automatically recognizes this and starts / stops the monitoring for the respective service. It is also possible at any time to change the configuration for the Services by updating the respective labels.
