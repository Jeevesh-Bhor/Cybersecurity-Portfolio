# CompTIA Security+ Notes

## Security Objectives

Triangle, with text label:
* Confidentiality
* Integrity
* Availability

---

## Security Controls

Measures which are implemented to protect an asset from intrusion or damage, the asset could be anything.

**For Example:**
* Data
* Physical Property
* Computer Systems etc.

### Control Categories

* Technical
* Managerial
* Operational
* Physical

#### Technical Controls
These are controls which are implemented with the systems such as:
* Firewalls
* Antivirus
* Encryptions
* Access Control Lists

#### Managerial Controls
These are basically administrative controls which implemented with respect to the security of the asset.

**For Example:**
* Security Policies
* Procedures
* Risk Assessments
* Governance Compliance

#### Operational Controls
These controls are implemented by people rather than systems.

**For example:**
* Guard Patrols
* Reception Desks
* Incident Response Procedures
* Security Awareness Programs

#### Physical Controls
These controls when implemented limit physical access of the intruder to the asset.

**For Example:**
* Locks
* Badge Readers
* Fences
* Cameras
* Guard Shacks

---

### Types of Controls

* Preventive Controls
* Deterrent Controls
* Detective Controls
* Corrective Controls
* Compensating Controls
* Directive Controls

#### Preventive Controls
This type of control is implemented to block access to the resource or asset.

**For Example:**
* Firewall Rules
* Security Policies
* Door Locks
* Guard Shacks

#### Deterrent Controls
This type of control is implemented to discourage or warn the intruder trying to get the unauthorized access to the resource or asset.

Making the intruder think twice on their actions they are about to take, it does not automatically blocks/prevents access to the resource or asset.

It is just a manipulation tactic put in place to play with the intruder's heads.

**For Example:**
* Splash Warning Screens
* Posted warning signs
* Reception Desks warning them

#### Detective Controls
This type of control is implemented to track, identify and log any attempt of intrusion in an unauthorized space.

This does not block/prevent access to the resource or asset but it gives useful information on the intrusion.

**For Example:**
* System Logs
* Login Reports
* Alerts from Badge Readers
* Motion Detectors

#### Corrective Controls
This type of control is implemented to control the damage or even reverse the impact of the event that is already done.

This control comes into effect after the intrusion has already happened.

**Example Scenario:**
If a computer system is infected with a ransomware, we can just clean the system completely and restore it using a clean backup.

**For Example:**
* Backups to restore the systems
* Creating new security policies to avoid the same security failure.

#### Compensating Controls
This type of control is implemented to compensate for the lack of primary safety measures which have failed due to some vulnerability.

These are basically other policies, or other secondary safety measures kept in place to prevent someone from exploiting the vulnerability until a permanent fix for it comes into play.

**For Example:**
* Monitoring 24/7
* Simultaneous guard duties 
* Implementing separation of duties

#### Directive Controls
This type of control is implemented to direct a subject to a security policy and compliance like Acceptable Use Policy.  

Basically putting in some safety and security procedures and standards to avoid security threats.

**For Example:**
* To segregate Confidential and Non-Confidential data.
* Posting "Authorized Personnel Only" sign which will enable someone to stop or enter the restricted space.
* Creating compliance policies and procedures
* Training users on proper security policies

---

## CIA Triad

* **C** - Confidentiality
* **I** - Integrity
* **A** - Availability

### Confidentiality
* Confidentiality means preventing disclosure of data to individuals or systems.
* Its basically preventing people from access data or information which they are unauthorized to fetch.

**For Example:**
* If we are sending some sensitive information to someone on the internet, we can encrypt the data which is being sent, and the receiver can then decrypt the data for their use.
* If anyone even manages to get the hold of the encrypted data, they won't be able to read it or decrypt it without the key.
* Another way we can implement confidentiality is by **Access Controls.** These are lists which state which individual or system is allowed to access the information or asset.
* We can even implement **Two-Factor Authentication** which is basically additional confirmation before the data is disclosed.

### Integrity
* Integrity is making sure that the information that is sent, received or stored cannot be modified without detection.
* We can implement this by:
  * **Hashing** - Where we can create a hash of the data that we are sending and we can send both the data and the hash to the recipient who can then run the same hash function to verify the integrity of the data that they have received.
  * **Digital Signatures** - These encrypt the hash in an asymmetrical encryption algorithm so that the recipient can both verify the integrity of the data they received and the sender.
  * **Certificates** - These are paired with both hashing and digital signatures and allow the recipient to additionally identify the devices from which the data was sent. This can be done if you are sending very sensitive information.
  * **Non Repudiation** - This is just giving proof of integrity of the data to the recipient so that they know the data they have received is genuine.

### Availability
* Availability is making sure that all the systems and networks are available all the time.
* We can implement this by:
  * Implementing systems which are built to run all the time (**Redundancy**).
  * This can also be done by implementing fault tolerances in the systems where if any part of the system is having any trouble or any faults then another systems can take up the job and keep the system running.
  * To ensure that the systems are available 24/7 we need to make sure that the systems are stable enough and we need to patch any security holes before they are exploited by attackers.

---

## Non-Repudiation

**Non-Repudiation** is giving proof of integrity and assurance that the data that has been sent is 100% genuine and has not been tampered with.

This can be implemented by:
* **Hashing** - Its purpose is to create the hash of the data that is to be sent.
  * Performing a hashing function on the data and sending both the data and the hash to the recipient.
  * The recipient can then run the same hashing algorithm on the data and compare their hash to the sender's hash.
* **Digital Signatures** - Its purpose is to identify the individual or the system that sent the data.
  * The hash of the data is generated.
  * The data's hash and the sender's private key are both encrypted to create a digital signature.
  * Next, both the data and the digital signature is sent to the recipient for them to decrypt and verify the integrity of the data.
  * Now this is done to both verify the source and the data received.
  * The decryption of the digital signature helps in verifying the source, because if the public key does not match the private key of the sender then it is likely that it was tampered with.

---

## AAA Framework

* **A** - Authentication
* **A** - Authorization
* **A** - Accounting

### Authentication
* Who we are?
* Authentication is basically claiming to be who we are.
* If we log into a system, we enter a username and when we enter the username that is us claiming to be that user.
* And when we enter the correct password for that user, then it is proved that we are who we are claiming to be and then the system lets us past the security.

### Authorization
* What access do we have based on our **Identification & Authentication?**
* Authorization is basically what places, features, actions, and operations we have access to based on our identification and what roles or permissions we have.

### Accounting
* Any action you take in the system will be recorded by the system.
* Any data sent or received, any actions takes by you, your log in/out times, any place inside the system where you visited will all be recorded and not go unnoticed.

### Authenticating Systems
* In an organization there are multiple computers and other systems on the network.
* To recognize each computer system and determine whether or not if its the organization's system or another system which should not be on the network.
* Here the authentication systems come into play.
* Authentication systems put a digitally signed certificate which is unique to every device in the organization.
* This way every device can be recognized and authenticated to be on the network and can safely be used by the organization.

### Certification Authentication
* In an organization there is always a **Certificate Authority**.
* The Certificate Authority is a device or a system which can both create and validate the certificate of the device.
* The Certificate Authority in itself is signed by a **root Certificate Authority**.
* We can then take the digital signature certificate and compare it to the Certificate Authority to check whether it was signed by the CA or not, which will help us in validating the identity of the device.

### Authorization Models
* After authentication comes Authorization Models.
* If we try to link every user to the data they need, then it might work for a smaller group of people.
* But it can quickly become a mess and impossible to manage once the number of people and data grows.
* To solve this issue, we put an authorization model in place, which defines all the roles, orgs, access to data etc.
* By doing this all the data is linked directly to the auth model rather than being linked individually to a user.
* After this we can simply put all the users in the auth model which will allow the users to access the data they need without it becoming a mess in the background.

---

## Gap Analysis

* It is the study of the gap between where you are and where you want to be.
* This process requires extensive research.
* This might take a long time considering the fact that we need to gather all the emails, data, and any kind of technical research to complete this analysis.
* **Choosing the framework** is also as important because we need to set a baseline or some kind of internal goals or standards that we are trying to achieve.
  * **For Example:**
    * **NIST Special Publication 800-171 Revision 2** - Protecting controlled classified information in non-federal systems and organizations
    * **ISO/IEC 27001** Information Security Management Systems
* **Evaluating People and Procedures** is also one of steps in gap analysis because we need to account for people's experience in the field, current training and their knowledge about compliance procedures etc.
* **Compare and Contrast** is where we need to evaluate all our systems, processes, procedures and have a detailed report on them.
  * This is mainly to check for weaknesses, and if any weaknesses are found we need to be ready with the measures to compensate for the weaknesses.
* **Analysis and Reports** is the final step.
  * We first define the baseline objectives and goals that we want to achieve.
  * Once we have gathered all the data on our systems, processes and procedures. All of their strengths and weaknesses.
  * We compile and put all this data into one final document which is the **GAP ANALYSIS REPORT.**
  * We will also need to implement set path to achieve the set standards, which will take time, money, a whole lot of adjustments in existing systems, calibration, purchase of new equipment to meet new set standards etc.

---

## Zero Trust

* In many networks, access to everything is very much open.
* But Zero Trust is a method which is applied by security admins, which is basically not trusting any individual, anything, or any system.
* We need to prove our identity at every action that we take or any resource that we are trying to access.
* We can implement this by, additional firewalls, encryptions, multi factor authentications etc.

### Planes of Operations
Where the network is split into two parts:
1. **Data Plane**
   * Data Plane is the system which processes all the frames, packets, and other network traffic.
   * It basically does what it needs to do to transport data from one part of the network to the other.
2. **Control Plane**
   * Control Plane is the control panel of the data plane.
   * Anything we need to control or determine on the data plane, can be done from the Control Plane.
   * We can define policies and rules.
   * Helps in determining how packets are forwarded etc.

### Controlling Trust
Where we implement additional security measures on the basis of what information we have gathered.
* Here we use something called **Adaptive Identity**.
* Adaptive Identity is basically trying to establish the identity of a user primarily on the basis of the credentials they provide.
* But we also check for additional information such as, type of connection they are trying to establish, their location, their relation to the organization etc.
* These additional information helps us in determining if the user is actually who they are and avoid any malicious activity.
  * **For Example:**
    * If an organization is based in the US and a user is trying to connect from China, this triggers those additional security measures.
    * Because it is unusual for someone to be related to an American organization and trying to connect from China.
    * So the system will try to establish if the user is who they are or not and then determine if they connection should be allowed or not.

### Security Zones
What gives us a broader security overview of the network connections instead of the one to one relationship of the networks.
* It gives a clear path of the connection, that is from where the connection started and where it is going.
* Using these security zones are enough to deny access to untrusted connections.
* So if I device is trying to connect from an untrusted network and trying to communicate with a device in the trusted zone, its access will be denied and not allowed to communicate with the device in the trusted network.

### Policy Enforcement Point (PEP)
Acts more like a gatekeeper.
* Any network traffic from devices going from untrusted to the trusted network shall always pass through PEP.
* So that the PEP can take decision on whether or not to allow or disallow this network traffic in the trusted network.
* PEP is just multiple devices working together trying to identity devices and their network traffic to determine if they are allowed to communicate or not.

### Applying Trust in Planes

#### Policy Decision Point
What is responsible to take the decision and authenticate every network request.
* The PEP gathers all the network requests and sends it to the PDP.
* PDP consists of two parts:
  1. **Policy Engine**
     * Policy Engine compares each network request to a set of predefine network policies set by the organization.
     * It takes the further decisions on the basis of those policies.
     * It can Grant, Deny or Revoke a network request of a device from an untrusted network.
  2. **Policy Administrator**
     * Policy Administrator is the one which communicates with the PEP.
     * It takes the decision made by the Policy Engine and communicates or passes it to the PEP.

---

## Physical Security

* It is a physical measure which is implemented to prevent people from gaining access to unauthorized areas of any facility.
* We can use:
  * Barriers/Bollards
  * Fences
  * Video Surveillance
  * Guards and Access Badges
  * Lighting
  * Sensors

### Barriers/Bollards
* These metal poles which are implemented to stop people from entering sensitive areas.
* It can be used to channel people into a specific access point.
* These can also act as a sign or notice that the are in which those are placed is a high security area if they are painted in bright color.

### Access Control Vestibules
* All doors are normally unlocked opening one door causes others to lock.
* All doors are normally locked, opening one door prevents others from unlocking.
* One door open/others locked - When one door is open other doors can't be opened.
* One at a time controlled groups are where people are allowed into an area one at a time rather than all together, similar system to a turnstile.

### Fencing
* These are implemented on the perimeter of any facility to prevent people from entering.
* We can install fences which are transparent to monitor outside the perimeter or we could install an opaque fence to block people from peeping inside.
* The fences need to be robust, so that people will be unable to bend or twist it to gain unauthorized access to the facility.
* The fences should also be tall and have razor wire at the top so that it is difficult for people to jump or climb over the fence.

### Video Surveillance
* We can use CCTVs or Closed Circuit Televisions to replace guards.
* These CCTVs are networked together and send their video feed straight to a singular storage point, where all the video that is being recorded can be stored.
* These cameras have capabilities like motion detection, face recognition and object detection which makes it effective to get clear view of faces, license or number plates.
* These can be used to alert or alarm people of an intrusion.

### Guard & Access Badges
* We can have guards in all major entrances/exits of the facility or even at reception desks so that these guards can validate if someone is an employee, client, guest or an intruder.
* It is also important to have two-person integrity, in order to prevent access to a physical asset in the hands of a single person.
* If more that a single person are having access to a physical asset then it becomes difficult to avoid or break any policy or rules for everyone.
* We can also use Access Badges which are just identification cards which every employee shall wear, these cards hold all the basic information about the employee.
* These cards can be used in badge readers which can be programmed with an access control list so that even employees cannot access certain parts of the facility unless the access is updated in their ACL.

### Lighting
* Lighting is also an essential part of security, because a well lit area helps keeping away the attackers.
* Attackers usually seek to attack in while being away from someone's view so they prefer dark areas.
* Lighting up the area properly with the proper lights and having the best lighting angle as possible if we are using cameras with facial recognition.
* We need to avoid shadows and glare and have good overall light levels.

### Sensors
There are multiple types of sensors:
* Infrared
* Pressure
* Microwave
* Ultrasonic

#### Infrared Sensors
* These which can see in both light and dark without lighting up the area.
* They are good for smaller areas and are common in motion detectors.

#### Pressure Sensor
* These which can be installed in the floors or windows.
* These can detect change in force.
* These can be used to alarm someone that there is someone or something moving or passing through a particular area of the facility.

#### Microwave Sensor
* These which can detect movement across a larger area.
* These work better than infrared in a larger area.

#### Ultrasonic Sensor
* Can send ultrasonic signals and received the sound waves back.
* These are more advanced kind of sensors.
* They can be used for detecting motion and also as collision detection.

---

## Deception & Disruption

* Deception and Disruptions are both an important part of IT Security, because protecting production grade systems are very important and can save millions in dollars from being lost in damages.
* We can implement this by means of:
  * Honeypots
  * Honeynets
  * Honeyfiles
  * Honeytokens

### Honeypots
* Virtualized systems which act as a fake vulnerable system which is not part of any production grade system, it is a decoy used by organizations to lure the attacker away from the actual target and keep them busy there.
* Honeypots can be created by multiple open source or enterprise solutions available in the market.
* Honeypots can be used to monitor the attacker and check for what kinds of attacks they are trying against the systems and network.
* We can use this information gathered to further enhance and improve our systems, so that they become more secure.
* As attackers become more aware about identifying honeypots, the more complex honeypots need to be constructed.

### Honeynets
* Just a bigger network of smaller honeypots.
* These include multiple servers, switches, workstations, firewalls etc.
* This large deception network with multiple devices networked together in a net make it a more realistic honeypot, and the one which will keep any attacker busy for a long time.

### Honeyfiles
* Basically files which are fake and used as bait to attract attackers.
* These files contain highly detailed yet fake information.
* So if a hacker stumbles upon these files they will expectedly open these and go through them for a long time.
* The files could be anything, from fake reports, to fake passwords, to fake salary records.
* Because these files are separate from the actual production network, no one from the organization can access it.
* If anyone does access these files, it will be highly likely an attacker snooping around in the honeypot, and an alert will be issued to the respective department or persons.

### Honeytokens
* Hidden trackers which can allow the security professionals to track a malicious actor.
* These tokens are hidden and embedded in files, when downloaded by an attacker, these will help track the attacker's Ip and other information.
* These can also be fake information like API credentials, or fake email ids, which we can monitor on the internet to pop up on any website or platform, which can help finding who might be attacking our systems.

---

## Change Management

* Change Management is basically managing how a change in introduced in company systems/networks.
  * **For Example:**
    * Upgrading Software
    * Changing firewall config
    * Updating active directory
    * Installing antivirus
* Each change has its own risks and to ensure safe operations and maximum availability of systems/networks, every change needs to undergo thorough review by the committee making these decision.
* Usually companies have a **Change Approval Process**, if a company does not have this procedure in place then it becomes difficult to implement new changes.

### Change Approval Process
A formal process to request a change in any systems or networks of the company.
* There is a formal process that everyone has to follow such as:
  * Filling out a change request form to the respective board.
  * In this form we determine the reason for the requested change.
  * Along with the purpose we provide the scope of change i.e. how many system/s are going to be affected by the change.
  * Schedule the time of change.
  * We then determine and analyze what the risks and the impact of the change will be on our systems or networks.
  * Finally we get approval/disapproval from the board to continue further.
  * If the changes are instated, we get end-user acceptance that the change has been implemented without any issues.

### Ownership
Are the people who manage the entire process of the change, they are kept informed about everything that is happening during the change in their systems/networks/applications/data etc. and once the change is complete, their responsibility is to check all systems run fine and the change has been done successfully.

**For Example:**
If a warehouse has a printer which is not running quiet well or needs an upgrade, then this work of upgrading the printer is done by the IT department while this entire process is being managed by whoever is in charge of the warehouse.

### Stakeholders
Are people who would want some input on the entire Change Management Process because ultimately the change may be at any scale, if it has chance to affect the company in any way then they are also affected. So they might want to seek input or have some control over certain processes or how things are carried out.

### Impact Analysis
Is basically accounting for every risk that the Change Management Process might introduce in the system. We need to see if anything breaks, if a change actually doesn't change anything, data corruption, other system failures etc.

* There are also risks with not making the change, such as:
  * Security Vulnerability
  * Application unavailability
  * Unexpected downtime

### Test Results
We can use a sandbox testing environment to test out the new changes, and observe if any new problems occur in the system/s. A sandbox is basically a safe environment to do all the tests and make sure everything is running properly with each other. Once all the changes are tested in the sandbox environment and approved, then we can implement then in a real production environment. We should also consider a backout plan because a sandbox environment cannot always account for every scenario, so we should always have a backout plan to restore our systems to a stable state if anything does happen to go wrong.

### Backout Plan
We always need a backout plan for every change management process. It is because it is not always the case that a change will work perfectly and nothing will go wrong. If anything does go wrong we need to have a backout plan which will help us reverse or restore the previous stable state of our systems/networks/devices/applications etc. Considering this scenario we should always have a backup of our systems as well which make it easier for us to rollback.

### Maintenance Window
These are one of the most tricky part of the jobs as we have to time the change management process properly so that the production environment does not get disturbed and ultimately availability is maintained. This can be done during the nights (difficult if its a 24/7 production environment) as doing this during a workday may not be the best option. We have to also consider yearly holidays, when the networks are frozen, which gives us plenty time to implement the change.

### Standard Operating Procedure
The **Change Management Process** is a lengthy process and affects many departments, systems and networks and to ensure that everything has been done correctly, everything should be well documented for future reference. The document should contain what systems were affected, what changed, what problem/s occurred if any, did the fix actually fix the problem that it was implemented for? etc. That one document would be helpful in the future if anything does go wrong.

---

## Technical Change Management
