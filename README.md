# Solar Village Project

## Overview:
Solar Village is an energy provider company. The following POC describes the functionality and implementation of the business processes associated with various steps of the requesting permits and their approval process.

Solar Village requires a 30 to 40-hour proof of concept (POC) using JBoss BPM Suite. The POC should be able to interact with business and technical associates at Solar Village.

## Repository Organization:
This repository consists of following modules

1. solarvillage-domainmodel
A java-maven based domain model that includes a java class for New Order Permit Requests. The jar file produced by this model is consumed by the processes created on Red Hat JBoss BPM Suite - business-central.

2. solarvillage-mockservice
A java-SpringBoot application project that mocks the government agencies' remote online services.
	- This mock services accepts new permit requests
	- Provides statuses of electrical and structural permit requests individually.
	- Allows cancellation of permit requests.
	- Allows rescinding permit requests.

3. SolarVillageProj
A RedHat JBoss BPM Suite based project that consume the domainmodel and mockservice, and consists of two processes - NewOrder and GovernmentPermit. The project can be build and deployed as KJar and processes could be started using business-central or intelligent server curl scripts.

## Getting Started:
### Installation:
1. Download RedHat JBoss EAP 7.x from here.
2. Download RedHat JBoss BPM Suite 6.4 for EAP 7 from here.
3. Follow section 2.2 of the BPM Suite Installation Guide to install BPM Suite on top of EAP.
4. Add users:
```
$ ./add-user.sh -a --user ragrahari --password password@1 --role kie-server,admin,rest-all,analyst,sales
$ ./add-user.sh -a --user execuser --password password@1 --role kie-server,admin,rest-all,analyst,executive
Start the EAP:
$ ./standalone.sh
```
### Set up Email Service:
- Start CLI and connect BPMS's server management server running on 127.0.0.1:9990
```
$ cd ....bpms-eap/bin/
$ ./jboss-cli.sh -c --controller=127.0.0.1:9990
```
- Set system property value:
```
[standalone@127.0.0.1:9990] /system-property=org.kie.mail.session:add(value="java:jboss/mail/Default")
```

- Set up the main subsystem:
```
[standalone@127.0.0.1:9990] /subsystem=mail/mail-session=default:write-attribute(name=from, value=bpms@solarVillage.org)
[standalone@127.0.0.1:9990] /subsystem=mail/mail-session=default/server=smtp:write-attribute(name=username,value=admin)
[standalone@127.0.0.1:9990] /subsystem=mail/mail-session=default/server=smtp:write-attribute(name=password,value=password)
```
- Configure mail-socket outbound socket binding to match settings of SMTP server listening on 127.0.0.1:2525
```
[standalone@127.0.0.1:9990] /socket-binding-group=standard-sockets/remote-destination-outbound-socket-binding=mail-smtp:write-attribute(name=host,value=localhost)
[standalone@127.0.0.1:9990] /socket-binding-group=standard-sockets/remote-destination-outbound-socket-binding=mail-smtp:write-attribute(name=port,value=2525)
[standalone@127.0.0.1:9990] exit
```
### Clone, build and install the *domainmodel* and *mockservice*
1. Clone the repository: https://github.com/ragrahari/neworderpermit.git 
2. Get into solarvillage-domainmodel folder and execute following: ```$ maven clean install```
3. Get into solarvillage-mockservice folder and execute following: ```$ maven clean install```

### Clone the SolarVillageProj KIE Project
1. Login to business-central using URL: http://localhost:8080/business-central and login using the authentication credentials previously added: ```ragrahari/password@1```
2. Add an organization unit. Go to Authoring -->  Administration and Click on Organization Unit --> Manager Organizational Units
3. Click on "Add" button to add an organization unit - ```solarvillage```
4. Go to Repositories --> Clone repository. Provide following information on the pop-up window:
```
Repository Name: neworderpermit-repo (or give any name of your wish)
Organizational Unit: solarvillage
Git URL: https://github.com/ragrahari/neworderpermit.git 
Username: *Leave blank*
Password: *Leave blank*
```

### Build the SolarVillageProj
1. In *business-central*, click on Authoring --> Project Authoring and select SolarVillageProj.
2. Click on *Open Project Editor* 
3. Select *Dependencies* to add the domainmodel jar file. Click on *Add* and provide the following GAV:
```
Group ID: ragrahari
Artifact ID: solarvillage-domainmodel
Version: 0.0.1-SNAPSHOT
```
4. Save the added dependency and then click on Build --> Build and Deploy
	You can validate the build on Deploy --> Process Deployments.
5. You can start processes from Process Management --> Process Definitions screen by starting NewOrder process.

### Setup KIE Server and KIE Container




## References:
- https://developers.redhat.com/products/eap/download/
- https://developers.redhat.com/products/bpmsuite/download/ 
- https://access.redhat.com/documentation/en-US/Red_Hat_JBoss_BPM_Suite/6.4/pdf/Installation_Guide/Red_Hat_JBoss_BPM_Suite-6.4-Installation_Guide-en-US.pdf 
