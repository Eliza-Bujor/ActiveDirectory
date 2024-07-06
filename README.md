# Active Directory

<h2>Description</h2>
The project involves building a home lab that will benefit both the blue team and general IT. I will demonstrate the step-by-step process of creating my own Active Directory environment, which will aid in understanding IT administration and the workings of a domain. Additionally, I will set up a standalone Splunk instance to ingest events from the Windows server hosting the Active Directory and the target Windows machine. At the end of the project, I will use Kali Linux as the attacking machine to perform a brute force attack, observing the generated telemetry, and also utilize Atomic Red Team.
<br /><br />
Active Directory
<ul>
  <li>Active Directory represents database that contains users, computers, groups, etc.</li>
  <li>Active Directory Domain Services represents database that contains objects (users, computers, groups, etc) and those objects will contain attributes (information about the object).</li>
  <li>Active Directory Domain Services (AD DS) should be installed, and then be promoted to Domain Controller (DC) in order to use Active Directory.</li>
  <li>DC will grant us the permission to perform authentication using the protocol called Kerberos.</li>
</ul>
<h2>Step 1: Build a Logical Diagram </h2>
<ol>
  <li>To create the diagram, I used the website app.diagrams.net, which offers built-in shapes that I utilized in the process.</li>
  <li>Splunk Universal Forwarder on the Active Directory Server and Target Machine - will allow me to send data to the Splunk Server.</li>
  <li>Sysmon on the Active Directory Server and Target Machine- to collect telemetry on the server and send it over to Splunk.</li>
  <li>Green dotted line from Target Machine to the Splunk Server - highlights the fact that data (logs) is being forwarded to the Splunk Server.</li>
  <li>Green dotted line from Active Directory Server to the Splunk Server - highlights the fact that data (logs) is being forwarded to the Splunk Server.</li>
  <li>Atomic Red Team - will help to generate data. This will be installed on the Target Machine.</li>
</ol> <br /> 
The diagram contains:<br />
<ul>
  <li>Splunk Server - Ubuntu 22.04</li>
  <li>Active Directory Server - Windows Server 2022</li>
  <li>Target Machine (Blue Colour) - Windows 10</li>
  <li>Attacker Machine (Red Colour) - Kali Linux</li>
  <li>Switch</li>
  <li>Router</li>
  <li>Cloud - Representing the Internet</li>
</ul>
<br />
<p align="center">
<img src="https://i.imgur.com/AO7JaMr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
  
<h2>Step 2: Install Virtual Machines </h2>
- <b>Windows Server</b> <br>
- <b>Windows 10</b> <br>
- <b>Kali Linux</b> <br>
- <b>Splunk using Ubuntu Server</b> <br>

<h2>Step 3: Install & Configure Software </h2>
- <b>Sysmon</b> <br>
- <b>Splunk</b> <br>

<h2>Step 4: Configure Active Directory </h2>

<h2>Step 5: Generate Telemetry with Kali Linux & ART </h2>
- <b>Kali Linux</b> <br>
- <b>Sysmon</b> <br>
- <b>Install Atomic Red Team</b> <br>
