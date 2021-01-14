#### Domain: Network Security

Suppose you have a firewall that's supposed to block SSH connections, but instead lets them through. How would you debug it?

Make sure each section of your response answers the questions laid out below. 

1. Restate the Problem
2. Provide a Concrete Example Scenario
   - In Project 1, did you allow SSH traffic to all of the VMs on your network?
   - Which VMs did accept SSH connections?
   - What happens if you try to connect to a VM that does not accept SSH connections? Why?
3. Explain the Solution Requirements
   - If one of your Project 1 VMs accepted SSH connections, what would you assume the source of the error is?
   - Which general configurations would you double-check?
   - What actions would you take to test that your new configurations are effective?
4. Explain the Solution Details
   - Which specific panes in the Azure UI would you look at to investigate the problem?
   - Which specific configurations and controls would you check?
   - What would you look for, specifically?
   - How would you attempt to connect to your VMs to test that your fix is effective?
5. Identify Advantages/Disadvantages of the Solution
   - Does your solution guarantee that the Project 1 network is now "immune" to all unauthorized access?
   - What monitoring controls might you add to ensure that you identify any suspicious authentication attempts?

Given the consideration that a firewall should be blocking SSH, but isn't, one should begin immediate diagnostics of the system. During the establishment of the ELK stack, SSH was permitted to be used to access all the VMs that were stood up in the process, as it meant that no specific passwords were needed. This was done with all of the VMs, with specific IP addresses only permitted. Attempting to connect without SSH would yield an error, as this was the only method of entry permitted in the NSG.

​	 However, if SSH traffic were presented to be a problem, one would have to set up NSG (Network Security Group) rules to prevent SSH traffic into the system. I would specifically check for rules permitting or denying ssh, and add or remove rules as needed. After that, all password settings that used SSH would have to be reset to manual passwords, so as to maintain accessibility to the VMs.

However, prohibiting SSH traffic does not, nor does anything else for that matter, guarantee that Project 1's ELK stack, or any other system, is free from unauthorized access. A logging system that tracks when the system was accessed, and a deny all that opens for a limited time window (ex. 5 minute window) do more to address the vulnerability of the system.

**Cloud Security**

How would you control access to a cloud network?

1. Restate the Problem
2. Provide a Concrete Example Scenario
   - In Project 1, did you deploy an on-premises or cloud network?
   - Did you have to configure access controls to this network?
   - What kinds of access controls did you configure, and why were they necessary?
   - How do these details relate to the interview question?
3. Explain the Solution Requirements
   - In Project 1, what kinds of access controls did you have to implement? Consider:
     - NSGs around the VNet? Around the VMs?
     - Local firewalls (ufw, etc.) on each VM?
     - Protocol allow/deny lists?
   - What did each access control achieve, and why was this restriction necessary for the project?
4. Explain the Solution Details
   - Which rules do you set for each NSG in the network?
   - How does access to the jump box work?
   - How does access from the jump box to the web servers work?
5. Identify Advantages/Disadvantages of the Solution
   - Does your solution scale?
   - Is there a better solution than a jump box?
   - What are the disadvantages of implementing a VPN that kept you from doing it this time?
   - What are the advantages of a VPN?
   - When is it appropriate to use a VPN?

How would cloud access be controlled in the use of the ELK stack?

In the process of assembling the VM network needed to run the ELK stack, all the machines were being run on a cloud network. Access controls were appropriately configured, so that only the host machine (the user's own computer IP) could access the cloud network exclusively. SSH would be only permitted through the aforementioned IP to the JumpBox Provisioner. In addition, SSH in was the only permitted traffic into the WEB1, WEB2, and ELK1 machines, from the JumpBox machine, with the exception of Port 5601 for the function of Kibana. This is to ensure that the system remains secure. The only other port open on the JumpBox machine was 80, to allow TCP through.

NSG's were placed to constrain the networks and prevent unwanted traffic, while permitting wanted traffic. On RedTeamNSG, an IP-only constraint  was placed to restrict inbound to the IP of the user's computer, making sure that nobody but the user could gain access. SSH was allowed so that each VM could be accessed via SSH. All SSH keys were specifically secured inside the password settings in the VM setup. Traffic from port was also permitted in to allow HTTP traffic into the system for the purposes of the DVWA. VNET inbound traffic was also permitted, so the user could easily move from one server to the next. This inbound traffic, and load balancer traffic, were the only traffic allowed into the RedTeamNSG. The only difference on the ELK1-NSG was the Port5601 traffic permitted in.

The ELK stack solution, done through a Jump Box, can scale, but in a limited way. Using one Jump Box can simplify the system, and make updates easy. Using a VPN is more accessible, but with that accessibility, comes more vulnerability. Full VPNs are harder to stand up than a simple jump box. A jumpbox is easier to seclude than a full featured VPN and easier to set up, but if it is compromised, it's a critical loss to the system.



How do you determine if a security event or alert  is important enough for escalation?

1. Restate the Problem
2. Provide a Concrete Example Scenario
   - What kinds of events and alerts did you encounter in Project 1?
   - Which of these events was most interesting or suspicious?
   - Why was the event suspicious? What led you to investigate it?
3. Explain the Solution Requirements
   - What do you need to figure out in order to determine if this event is worth escalating?
4. Explain the Solution Details
   - How did you use Kibana to find this information?
5. Identify Advantages and Disadvantages of the Solution
   - How confident are you in your conclusion?
   - What additional data would be useful to determine if your conclusions are correct?

Determining if there a security alert needs to be escalated has a few factors. Frequently, traffic connected to a security event will be seen at odd hours, or will be seen with machines trying to connect to machines they normally wouldn't be connecting to, inside or outside the network. For example, a machine with traffic of 9,906 bytes of data on a Windows XP machine, does sound to be a particularly odd, and merits some further investigation. Ultimately, you need to determine more about the traffic that you are getting and see if more context, such as the contents of the data, will inform the next steps. Kibana will help to provide the raw metrics of the data to help you give a starting point on continued investigation. Ultimately, in combination with other tools, you can determine a huge amount of information about a potential security event.