# Guide for Test Based Certification Tools

Authors: Filippo Gaudenzi

Dipartimento di Informatica Università degli Studi di Milano Crema, Italy, 26013

##Introduction
This repository hosts CUMULUS CertifIcation Models (CMs) for the test-based certification architecture prototype described in the project Deliverable D3.3.
The certification architecture is composed of a TestManager and a TestAgent that are available as VMs on our ftp repository (see Deliverable D3.3). 
Once the whole system is up and running is possible to load the CMs to see how advance certification processes are managed and executed.

The three types of advanced certifications are:
	- Hybrid Certification
	- Composition of Certificates
	- Incremental Certification

Please, in order to interact with the TestManager SOAP interface, use Firefox and install SOA Client (https://addons.mozilla.org/it/firefox/addon/soa-client/). To connect to Test Manager Interface set the WSDL address as http://<testmanagerIP>:8080/CumulustestManager-0.0.1-SNAPSHOT/services/TestManagerAPI?wsdl. If evertything is fine you should see a dashboard as in Figure 1:

![alt text](https://raw.githubusercontent.com/fgaudenzi/TBprototypeInstallation/master/Others/SOA.png "Figure 1")Figure 1


The Test Manager comes alredy with 2 certificates stored in its Database. Both Certificates refer to the same proprety "Storage-confidentiality" and address two OpenStack deployment hosted at UMIL. The related CM are:

- [CM Openstack 1](https://raw.githubusercontent.com/fgaudenzi/TBprototypeInstallation/master/OpenStack1.xml)
- [CM Openstack 2](https://raw.githubusercontent.com/fgaudenzi/TBprototypeInstallation/master/OpenStack1.xml)

**CM Openstack 1**  produces evidence that satisfies the property, indeed the Certificate is in ISSUED state. 
	
	It is possible to check it running the operation 6 : getCertificate_Testing from the SOA Client using as ID=1432910969401

**CM Openstack 2** produces evidence that doesn't satisfy the property, and its certificate is in REVOKED state.
	
	It is possible to check it running the operation 6 : getCertificate_Testing from the SOA Client using as ID=1432911193779


## Hybrid Certification

Hybrid Certification involves both test and monitor based CM. The CM available [here](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Hybrid/HybridCM.xml) uses both testing and monitoring approaches in order to certify the property *SEF:incident-management-quality:percentage-of-timely-incident-resolutions*. The CM *i)* first executes a test in pre-deployment environment and then *ii)* asks Monitor Manager to check the Target of Certification for the same property. Only if test and monitoring activities went fine the certification is Issued.

Download the CM and using the SOA Client add it to the Test Manager repo using the operation 4: addCm_Testing. You need to copy-paste the whole XML as parameter and then click Invoke.

Once the CM is added you can start the certification process by executing operation 9: requestCertificate_Testing specifying the CM id. The result you get is the certificate ID that you will use to retrieve the certificate.

To have the whole flow of action please control logs at */var/log/tomcat7/catalina.out* on the Test Manager and at */var/log/testagent/celery.log* on the Test Agent.


## Certificate Composition

As stated in the introduction section, Test Manger already comes with two certificates. In this section we will use these certificates to build a composite certificate that certifies Welight VM for data-leakage prevention (Welight is a CUMULUS official pilot). Welight is hosted on OpenStack and the property relies on two mechanisms: an encrypted storage and an encrypted channel. The encrypted channel is tested directly by the Test Agent, while the encrypted storage is guaranteed by the presence of a certificate for the OpenStack hosting Welight for the Storage Confidentiality property.

CM is available at:

- [CM1] (https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Composition/composition-test1.xml)


Download the CM and using the SOA Client add it to the Test Manager repo using the operation 4: addCm_Testing. You need to copy paste the whole XML as parameter and then click Invoke.

Once the CM is added you can start the certification process by executing operation 9: requestCertificate_Testing specifying the CM id. The result you get is the certificate ID that you will use to retrieve the certificate.

To have the whole flow of action please control logs at */var/log/tomcat7/catalina.out* on the Test Manager and at */var/log/testagent/celery.log* on the Test Agent.

## Incremental Certification

Based on the certificate composition described in previous section, we simulate a Welight VM migration from an OpenStack1 to an Openstack2. Indeed, all the mechanisms remain the same, but the configuration may change. In this scenatio the Test Manager will receive the modified CM after the migitation, it will recognize the changes w.r.t. the prevoius CM before the migration and it will ask to Test Agent to run only the changed test cases (i.e. incremental certification).

The CMs that represents the Certification before and after the migration are available at:
- [before migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/beforeMigration.xml)
- [after migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/afterMigration.xml)


Download the CM [before migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/beforeMigration.xml)
 and using the SOA Client add it to the Test Manager repo using the operation 4: addCm_Testing. You need to copy paste the whole XML as parameter and then click Invoke.

Once the CM is added you can start the certification process by executing operation 9: requestCertificate_Testing specifying the CM id. The result you get is the certificate ID that you will use to retrieve the certificate.

After few seconds, you can update the uploaded CM with a new one  representing the state after the migration event.


Update the CM using again the SOA Client. Download the CM [after migration](https://github.com/fgaudenzi/TBprototypeInstallation/blob/master/Incremental/afterMigration.xml) 
and execute  the operation 4: addCm_Testing. You need to copy paste the whole XML as parameter and click Invoke. Once the Test Manager receive the new CM it will compare the old and the new ones to identify the differences. If before, Test Manager required to run both cchannel and ccertificate collectors, now it will require to the Test Agent to run only the ccertificate collector that is effected by the migration.
Being the new Openstack not certified for the required property the certificate will change from ISSUED to REVOKED.


To have the whole flow of action please control logs at */var/log/tomcat7/catalina.out* on the Test Manager and at */var/log/testagent/celery.log* on the Test Agent.
