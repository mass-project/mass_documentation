MASS Autoscaler
===============

Introduction
------------
The MASS Autoscaler is a tool for the MASS server that provides the functionality to automatically create or quit replicas of running Analysis Instances, depending on current demand. This ensures that no unnecessary number of unused instances are executed. Here, not only the current demand is considered, but also that of an adjustable period.

Requirements
------------

A requirement for using the MASS Autoscaler are running Analysis System Instances in Docker Containers managed by a Docker Service. You can get more information about this topic in the Docker `Documentation. <https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/>`_

It is important for the MASS Autoscaler that the current services have labels that indicate that this service belongs to a MASS Analysis System. These labels provide information about the service, such as the Analysis system, the minimum and maximum number of instances, etc. . A service can be easily labeled via the Docker compose file while creation. In the following you will find a sample file that shows which form these labels must have.

.. literalinclude:: compose.txt

Keep in mind that undefined labels are automatically interpreted as default values.


Configuration & Start
---------------------
The next step is to configure the Autoscaler. This is done by configuring the settings and defining the API Key.
To configure the Autoscaler you have to adjust the entries in the *config.py* file. At the beginning it should be enough to define the server address. Explanations of the individual options can also be found in the configuration file.

The setting of the API Key is realized by an environment variable. To archieve this, the Autoscaler has to be started by the following input in the command line:


.. code-block:: bash

	API_KEY=<api_key> python autoscaler.py

Now the autoscaler monitors the running services and adjusts the replica number based on the number of Analysis Requests.

During runtime, additional services can be started and existing services can be terminated at any time. The Autoscaler automatically recognizes this and starts / stops the monitoring for the respective service. It is also possible at any time to change the configuration for the services by updating the respective labels.