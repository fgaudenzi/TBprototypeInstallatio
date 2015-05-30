# Guide for Test Based Certification Tools

Authors: Filippo Gaudenzi

Dipartimento di Informatica Università degli Studi di Milano Crema, Italy, 26013

##Introduction
This repositories hosts CMs to be used on the the prototype availabe as explained in D3.3 Annex.

TestManager and TestAgent are available as VMs on the UMIL ftp repository.
Follow the guide on the annex to make them run.

Once the whole system is up and running is possible to use the CMs here available, to see how advance certification process are managed and executed.

The three type of advanced certifications hosted here are:
	- Hybrid Certification
	- Composition of Certificates
	- Incremental Certification

Please, in order to interact with the TestManager SOAP interface, use Firefox and install SOA Client (https://addons.mozilla.org/it/firefox/addon/soa-client/). To connect to Test Manager Interface set the WSDL address as http://<testmanagerIP>:8080/CumulustestManager-0.0.1-SNAPSHOT/services/TestManagerAPI?wsdl. If evertything is fine you should see a dashboard as in Figure 1:

![alt text](https://raw.githubusercontent.com/fgaudenzi/TBprototypeInstallation/master/Others/SOA.png "Figure 1")Figure 1


The Test Manager comes alredy with 2 certificate stored in its Database. Both Certificate refers to the same proprety "Storage-confidentiality" and address two OpenStack deployment hosted at Unimi. The related CM are at:

- [Openstack 1](https://raw.githubusercontent.com/fgaudenzi/TBprototypeInstallation/master/OpenStack1.xml)
- [Openstack 2](https://raw.githubusercontent.com/fgaudenzi/TBprototypeInstallation/master/OpenStack1.xml)

**Openstack** 1 satisfys the property, indeed the Certificate is in ISSUED state. 
	
	It is possible to check it running the operation 6 : getCertificate_Testing from the SOA Client using as ID=1432910969401

**Openstack 2** doesn't satisfy the property, and its certificate is in REVOKED state.
	
	It is possible to check it running the operation 6 : getCertificate_Testing from the SOA Client using as ID=1432911193779


## Hybrid Certification

Hybrid Certification involves both test and monitor. The CM available [here](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Hybrid/HybridCM.xml) uses both testing and monitoring approach in order to ceritify	the property *SEF:incident-management-quality:percentage-of-timely-incident-resolutions*. The CM before execute a test in pre-deployment environement and then ask to monitoring to check the Target for the same property. Only if test and monitoring activities went fine the Target is Issued.

Download the CM and using the SOA Client add it to the Test Manager repo using the operation 4: addCm_Testing. You need to copy paste the whole XML as paramenter and click Invoke.

Once the CM is added you can start the certification process by executing operation 9: requestCertificate_Testing specifying the CM id. The result you get is the certificate ID that you will use to retrive the certificate.

To have the whole flow of action please control log at */var/log/tomcat7/catalina.out* on the test manager and at */var/log/testagent/celery.log*


## Certificate Composition

As stated in the introduction section, TestManger already comes with two certificate. In this section we will use these certificate to built a composite certificate that certifies Welight machine for data-leackage prevention. Welight is hosted on OpenStack and the property relies on two mechanism: an encrypted storage and an encrypted channel. The encrypted channel is tested directly by the Test Agent, while the encrypted storage is guranteed by the presence of a certifcate for the OpenStack hosting welight about Storage Confidentiality.

The CMs are available at:

- [CM1] (https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Composition/composition-test1.xml)
- [CM2] (https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Composition/composition-test2.xml)

Download the CM and using the SOA Client add it to the Test Manager repo using the operation 4: addCm_Testing. You need to copy paste the whole XML as paramenter and click Invoke.

Once the CM is added you can start the certification process by executing operation 9: requestCertificate_Testing specifying the CM id. The result you get is the certificate ID that you will use to retrive the certificate.

To have the whole flow of action please control log at */var/log/tomcat7/catalina.out* on the test manager and at */var/log/testagent/celery.log*

## Incremental Certification

Based on the certificate composition from the previous section, we can hypothesize that Welight VM migrate from an OpenStack1 to an Openstack2. Indeed, all the mechanisms remain the same, but the configuration may change. Test Manager will receive the modified CM, will recognize the changes and it will ask to Test Agent to run only the changed test cases.

The CMs that represents the Certification before and after the migrotio are available at:
- [before migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/beforeMigration.xml)
- [after migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/afterMigration.xml)


Download the CM [before migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/beforeMigration.xml)
 and using the SOA Client add it to the Test Manager repo using the operation 4: addCm_Testing. You need to copy paste the whole XML as paramenter and click Invoke.

Once the CM is added you can start the certification process by executing operation 9: requestCertificate_Testing specifying the CM id. The result you get is the certificate ID that you will use to retrive the certificate.

After few seconds, you can update the uploaded CM with a new one that still adresses the Welight VM but where the Openstack references are changed. Indeed, the new CM ask for a different certificate related to a different OpenStack Deployment, that one hosting the Welight VM right now.

Update the CM using again the SOA Client. Download the CM [after migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/afterMigration.xml)
and excute  the operation 4: addCm_Testing. You need to copy paste the whole XML as paramenter and click Invoke. Once the Test Manager receive the new CM it will compare the old and the new ones to identify the differences. If before, Test Manager required to run bot cchannel and ccertificate collector, now it will require to test agent to run only the ccertificate because the only change is about the hosting IaaS.
Being the new Openstack not certified for the required property the certificate will change from ISSUED to REVOKED.


To have the whole flow of action please control log at */var/log/tomcat7/catalina.out* on the test manager and at */var/log/testagent/celery.log*
