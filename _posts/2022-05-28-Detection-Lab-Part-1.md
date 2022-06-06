---
layout: post
title: "Detection Lab: The long way (Pt. 1)"
author: gaz
categories: [ HomeLab, DetectionLab, ThreatHunting ]
---
<h2>Introduction</h2>

I recently came across [Chris Long](https://twitter.com/Centurion)'s [Detection Lab](https://github.com/clong/detectionlab). Detection Lab automates the creation of an Active Directory environment, complete with logging and additional tools. Once deployed it acts as a play area for defenders to practise and test, all whilst capturing the sort of logs you’d expect from any self-respecting business. I can’t tell you how many times I’ve wanted to set up a similar environment but not known where to begin. 

However, after trying to deploy the environment, it became obvious that whilst a lot of time had been put into the automation and additional features, not a huge amount was spent on documentation. A statement that some of the project contributors have openly admitted and vowed to rectify. After eventually admitting defeat, I realised that the better solution would be to configure the environments manually. Since the documentation details the endpoints and installed tools, it takes away the main struggle I was facing.

<p align="center">
<img src="/assets/images/lab.png" alt="alt text" height="600" >
</p>

This diagram gives an overview of what a deployed Detection Lab environment should look like. Since I’m creating this manually, I’ll likely omit some of the tools and feed the logs into IBM QRadar Community Edition. To save some time I’ve already created the required virtual machines in ESXi. So, we’ll start by promoting one of the Windows 2019 servers to a domain controller.

<h2>Installing Roles & Features</h2>
We’ll start small by modifying the Local Security Policy to enable “Interactive logon: Do not require CTRl + ALT + DEL”. This just makes logging into the server a little easier when RDP’ing.

Firstly, head to Tools > Local Security Policy from the Server Manager window.
<p align="center">
<img src="/assets/images/localsecuritypolicy.png" alt="Local Security Policy" height="300" >
</p>
Then open and enable “Interactive logon: Do not require CTRl + ALT + DEL”, located under Local Policies > Security Options.
<p align="center">
<img src="/assets/images/noCAD.png" alt="No CTRL + ALT + DEL" height="300">
</p>
Now that’s sorted, we need to install the services and software required for a DC. We can do this by selecting “Add roles and features” from the quick start section of the Server Manager dashboard.
<p align="center">
<img src="/assets/images/dashquickstart.png" alt="Dashboard Quick Start" height="300">
</p>
We’re then presented with two installation types:

<b>Role-based or feature-based installation:</b> This option allows you to select which services/roles you want to install.
<p>
<b>Remote Desktop Services installation:</b> This option will select, install, and configure the needed components for the selected scenario.
<p>
We'll choose "Role-based or feature-based installation" and keep the default settings on the Server Selection page.
<p>
To promote the server to a domain controller we need to ensure that "Active Directory Domain Services" are installed, at the very least.
<p align="center">
<img src="/assets/images/addroles.png" alt="Roles/Services" height="500">
</p>
Select the check box next to the service and click Add Features on the pop-up. On the next page, the basic features required for this role should be selected by default. Click next and next again.
<p align="center">
<img src="/assets/images/confirm.png" alt="Confirmation page" height="500">
</p>
Finally, ensure the automatic restart option is enabled and click install.

<h2>Configuration</h2>
Now that the roles and features are installed, we need to promote the server to a domain controller. First, open the notifications window and select "Promote this server to a domain controller".
<p align="center">
<img src="/assets/images/promote.png" alt="Promote to domain controller" height="300">
</p>
Next, select "Add a new forest" and specify a domain. This is commonly a .local address.
<p align="center">
<img src="/assets/images/newforest.png" alt="Add a new forest" height="500">
</p>
Then we need to set a DSRM (Directory Services Restore Mode) password. DSRM is a boot mode that allows repair or recovery of Active Directory. It allows logon when Active Directory has failed.
<p align="center">
<img src="/assets/images/options.png" alt="Specify DSRM password" height="500">
</p>
We can ignore the warning on the DNS Options page since we're configuring a DNS server as part of this process. Leaving this page as is and moving to the next step.
<p>
After a few seconds, the NetBIOS name should automatically populate.
<p align="center">
<img src="/assets/images/additionaloptions.png" alt="Set netBIOS name" height="500">
</p>
The paths can be left as default.
<p align="center">
<img src="/assets/images/paths.png" alt="Set paths" height="500">
</p>
Finally, review the options and, once the prerequisite checks are complete, click install.
<p align="center">
<img src="/assets/images/prereq.png" alt="Prerequisit checks" height="500">
</p>
Now we have the domain controller configured. We'll look to add the other two VMs to the domain and configure the event collector in the next part.