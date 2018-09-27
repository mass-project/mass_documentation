Multistaged Analysis
====================

The Multistaged Analysis is a tool integrated in the MASS-API-Client which allows the implementation of an analysis module consisting of an analysis pipeline.

Each step of the pipeline is called a Stage. Each Stage has its own incomming queue.
A intended workflow for the pipeline is, to receive an Analysis Request with its Sample, process an analysis consisting of one or more steps, and finally send the report back to the MASS-Server.

Minimal Process-Based Pipeline Examples
---------------------------------------
This is a minimal process-based analysis pipeline example which consists of three stages:

1. get requests: Get the Scheduled Requests form the MASS-Server. This Stage uses the function :func:`~mass_api_client.utils.multistaged_analysis.miscellaneous.get_requests` .
2. analyze_domain_length: Analyze the length of the domain Sample.
3. report: Send the analysis results back to the MASS-Server. This Stage uses the function :func:`~mass_api_client.utils.multistaged_analysis.miscellaneous.report` .

In this Case each stage runs independently in its own Process. You can easily scale the number of replicas of a stage by using the 'replicas' parameter of the :func:`~mass_api_client.utils.multistaged_analysis.AnalysisFrame.add_stage` function.

.. code-block:: python

	from mass_api_client import ConnectionManager
	from mass_api_client.utils import get_or_create_analysis_system
	from mass_api_client.utils.multistaged_analysis import AnalysisFrame
	from mass_api_client.utils.multistaged_analysis.miscellaneous import get_requests, report


	def analyze_domain_length(sockets):
	    data = sockets.receive()
	    sample = data.sample
	    if sample.has_domain():
		domain = sample.unique_features.domain
		length = len(domain)
	    else:
		length = 0
	    data.report_json(sockets, {'domain_length': length})
	    sockets.send(data)


	if __name__ == '__main__':
	    ConnectionManager().register_connection('default', <api_key>, <server_address>)
	    analysis_system = get_or_create_analysis_system(identifier='domain_analyzer',
		                                            verbose_name='domain_analyzer',
		                                            tag_filter_exp='sample-type:domain'
		                                            )
	    frame = AnalysisFrame()
	    frame.add_stage(get_requests, 'get_requests', args=(analysis_system,),
		            next_stage='analyze_domain_length')
	    frame.add_stage(analyze_domain_length, 'analyze_domain_length', next_stage='report', replicas=2)
	    frame.add_stage(report, 'report')
	    frame.start_all_stages()


Adding Async Stages
-------------------
You can start stages based on coroutines by passing the concurrency='async' parameter to the 'add_stage()' function. 


.. code-block:: python

	from mass_api_client import ConnectionManager
	from mass_api_client.utils import get_or_create_analysis_system
	from mass_api_client.utils.multistaged_analysis import AnalysisFrame
	from mass_api_client.utils.multistaged_analysis.miscellaneous import get_requests, report


	async def analyze_domain_length(sockets):
	    data = await sockets.receive()
	    sample = data.sample
	    if sample.has_domain():
		domain = sample.unique_features.domain
		length = len(domain)
	    else:
		length = 0
	    data.report_json(sockets, {'domain_length': length})
	    await sockets.send(data)


	if __name__ == '__main__':
	    ConnectionManager().register_connection('default', <api_key>, <server_address>)
	    analysis_system = get_or_create_analysis_system(identifier='domain_analyzer',
		                                            verbose_name='domain_analyzer',
		                                            tag_filter_exp='sample-type:domain'
		                                            )
	    frame = AnalysisFrame()
	    frame.add_stage(get_requests, 'get_requests', args=(analysis_system,),
		            next_stage='analyze_domain_length')
	    frame.add_stage(analyze_domain_length, 'analyze_domain_length', concurrency='async', next_stage='report')
	    frame.add_stage(report, 'report')
	    frame.start_all_stages()

.. note::
	Note that each stage runs on the same event loop. If a stage should run on its own loop, you can implement this solution inside a process based stage.



The Create-Sample-Pattern
-------------------------
In the example above you can see the usage of default stages based on the functions :func:`~mass_api_client.utils.multistaged_analysis.miscellaneous.get_requests`  and :func:`~mass_api_client.utils.multistaged_analysis.miscellaneous.report` . They are implemented to realize the workflow of 'normal' analysis systems: Get a sample and report from the MASS-Server, process the analysis and Report the results to MASS.

Another Pattern, implemented in this module, is to create samples locally and submit them to MASS. This functionality is implemented in the function :func:`~mass_api_client.utils.multistaged_analysis.miscellaneous.create_sample_and_report` . You can see below a minimal example demonstrating this pattern. The MASS-CT-Crawler is also based on this pattern.

.. code-block:: python

	import time

	from mass_api_client import ConnectionManager
	from mass_api_client.utils import get_or_create_analysis_system
	from mass_api_client.utils.multistaged_analysis import AnalysisFrame, CreateSampleAndReportObject
	from mass_api_client.utils.multistaged_analysis.miscellaneous import create_sample

	i = 0


	def domain_generator(sockets):
	    global i
	    data = CreateSampleAndReportObject(sample_domain='thisisadomain{}.com'.format(i))
	    i += 1
	    sockets.send(data)
	    time.sleep(3)


	if __name__ == '__main__':
	    ConnectionManager().register_connection('default', <api_key>, <server_address>)
	    analysis_system = get_or_create_analysis_system(identifier='domain_creator',
		                                            verbose_name='domain_creator',
		                                            tag_filter_exp='sample-type:domain'
		                                            )
	    frame = AnalysisFrame()
	    frame.add_stage(domain_generator, 'domain_generator', next_stage='create_sample')
	    frame.add_stage(create_sample, 'create_sample', args=(analysis_system,))
	    frame.start_all_stages()




