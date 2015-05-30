# Guide for Test Based Certification Tools

Authors: Filippo Gaudenzi

Dipartimento di Informatica Università degli Studi di Milano Crema, Italy, 26013


This repositories hosts CMs to be used on the the prototype availabe as explained in D3.3 Annex.

TestManager and TestAgent are available as VMs on the UMIL repository.
Follow the guide on the annex to make them run.

Once the whole system is up and running is possible to use the CMs here available, to see how advance certification process are managed and executed.

The three type of advanced certifications hosted here are:
	- Hybrid Certification
	- Composition of Certificates
	- Incremental Certification

Please, in order to interact with the TestManager SOAP interface, use Firefox and install SOA Client (https://addons.mozilla.org/it/firefox/addon/soa-client/). To connect to Test Manager Interface set the WSDL address as http://<testmanagerIP>:8080/CumulustestManager-0.0.1-SNAPSHOT/services/TestManagerAPI?wsdl. If evertything is fine you should see a dashboard as in Figure 1:

![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title Text 1")


The Test Based Certification comes already with 2 Certificates inside


Probes
	
	Authentication Confidentiality
		probe - cfile
		probe - https_Driver

	Storage Confidentiality
		probe - testEncryption
		probe - findClearMapper

	Network Isolation
		probe - sgnetwork	

	Storage Performance
		probe - writeReadVolumes

	Service Performance
		probe - novacinderUnderHD (Test_ambiente.sh+ciclo_richieste.sh)


The Test Based Certification Framework is available at
- Test Manager : https://github.com/fgaudenzi/testManager
- Test Agent   : https://github.com/fgaudenzi/testAgent

