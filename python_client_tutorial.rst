MASS Python Client: Tutorial
============================

Managing connections
--------------------

To get started with using the python client, you first need to define a connection.

>>> import mass_api_client as mac
>>> mac.ConnectionManager().register_connection('default', '<your api key>', '<mass server url>')

The connection with the alias 'default' will be used for every request, unless you use `SwitchConnection <connection_reference.html#mass_api_client.switch_connection.SwitchConnection>`_


Handling resources
------------------

All samples, reports and other objects on the server can be managed with the resources in the client.
Every resource supports the creation and retrieval of objects on the server.

To create objects, every resource has a :func:`create` method with specific parameters.
So for example to create a new URISample on the server you could simply use this:

>>> from mass_api_client.resources import *
>>> URISample.create('https://mass-project.github.io/')
[URISample] 5980d97115b77f1097d2dce6

As you can see, the :func:`create` method also returns the newly created object.
Or to give another example of uploading a sample file:

>>> with open('sample.exe', 'rb') as fp:
...     FileSample.create('sample.exe', fp)
...
[ExecutableBinarySample] 597527e215b77f2e9192337e

There are two ways of retrieving objects from the server.
The first one is two get a single object by its identifier with :func:`get`.
The identifier is usually the `id` of the object, as you can see here:

>>> Sample.get('597f721c15b77f0f23d16c0e')
[FileSample] 597f721c15b77f0f23d16c0e

But there are also some resources, which have another unique identifier, like their name.
Resources with different identifiers are :class:`AnalysisSystem`, :class:`AnalysisSystemInstance` and :class:`SampleRelationType`.

>>> AnalysisSystem.get('ssdeep')
[AnalysisSystem] ssdeep

The second way to retrieve objects, is to query them by specific filter parameters.
Therefore you can use :func:`query`, which returns a iterator over the matching objects:

>>> samples = Sample.query(delivery_date__gte=datetime.datetime.now() - datetime.timedelta(days=2))
>>> list(samples)
[[URISample] 5980d97115b77f1097d2dce6, [FileSample] 597f721c15b77f0f23d16c0e, [FileSample] 597f721715b77f0f23d16c06, [FileSample] 597f720c15b77f0f23d16c01, [FileSample] 597f4a5015b77f0f23d16768, [FileSample] 597f4a4b15b77f0f23d16764, [FileSample] 597f4a4415b77f0f23d16760, [FileSample] 597f4a3d15b77f0f23d1675c]

You can also use multiple parameters at once:

>>> FileSample.query(file_size__gte=50000, tags__all='has-callgraph')

The available filter parameters are resource specific and currently only implemented for samples.
To find a list of available filters and also other resource specific methods, please take a look at `Resources <resources_reference.html#Resources>`_.



Implementing analysis systems
-----------------------------
<TODO>

.. code-block:: python

    import os
    from mass_api_client import ConnectionManager
    from mass_api_client.utils import process_analyses, get_or_create_analysis_system_instance

    def size_analysis(scheduled_analysis):
       sample = scheduled_analysis.get_sample()
       with sample.temporary_file() as f:
           sample_file_size = os.path.getsize(f.name)

        size_report = {'sample_file_size': sample_file_size}
        scheduled_analysis.create_report(
            json_report_objects={'size_report': ('size_report', size_report)},
            )

    if __name__ == "__main__":
        ConnectionManager().register_connection('default', 'your api key', 'mass server url')

        analysis_system_instance = get_or_create_analysis_system_instance(identifier='size',
                                                                          verbose_name= 'Size Analysis Client',
                                                                          tag_filter_exp='sample-type:filesample',
                                                                          )
        process_analyses(analysis_system_instance, size_analysis, sleep_time=7)
