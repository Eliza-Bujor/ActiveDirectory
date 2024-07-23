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
Steps: <br />
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
  <li>Splunk Server - Ubuntu 24.04</li>
  <li>Active Directory Server - Windows Server 2022</li>
  <li>Target Machine (Blue Colour) - Windows 10 Pro</li>
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
OS to be installed:<br />
<ul>
  <li>Windows Server 2022</li>
  <li>Windows 10 Pro</li>
  <li>Kali Linux</li>
  <li>Ubuntu 24.04</li>
</ul>
Steps: <br />
<ol>
  <li>Download the ISO files of the OS mentioned above.</li>
    <p align="center">a. Windows 10 Pro<br />
    <img src="https://i.imgur.com/nd17Dn7.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
    b. Kali Linux<br /><p align="center">
    <img src="https://i.imgur.com/RbGf5Ju.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
    c. Windows Server 2022 <br /><p align="center">
    <img src="https://i.imgur.com/NTJigBn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
    <img src="https://i.imgur.com/DgMWyQr.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br /><p align="left">
     * I have selected the 2nd option as it will offer a Desktop mode experience rather than a CLI. <br />
     * As the goal is to perform a simple active directory lab, there is no need to download the Datacentre version as it contains advanced features and can be useful if to host many virtual machines on it.<br />
    <p align="center"><img src="https://i.imgur.com/a68sHJG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
    <img src="https://i.imgur.com/C1zRs3h.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
    d. Ubuntu 24.04 <br />
    <p align="left"> * In comparison to the other OS, I have set the Ubuntu Server to contain 8GB of RAM, 2 Processors and a Disk Size of 100GB as this represents the Splunk Server and it will ingest a lot of data (from AD Server and Target Machine) and I will be running searches on it. <br />
    <p align="center">
    <img src="https://i.imgur.com/xgdes8W.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
    <p align="left"> 
      * Command: sudo apt-get update && sudo apt-get upgrade -y <br />
      * This command will update and upgrade all the repositories. <br />
    <p align="center">
    <img src="https://i.imgur.com/dx7jPjn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br />
</ol>


<h2>Step 3: Install & Configure Software </h2>
Software to be installed: <br />
<ul>
  <li>Sysmon</li>
  <li>Splunk</li>
</ul>
Steps: <br />
<ol>
  <li>Setup NAT Network</li>
    <ul>
      <li>Set the network settings to NAT network to ensure that the virtual machines are set up to the same network and have internet access.</li>
      <li>Click Tools -> Bullet points -> Network -> NAT Networks -> Create -> Set the IPv4 Prefix to the one set up in the diagram -> Apply</li>
    </ul>
    <p align="center">
      <img src="https://i.imgur.com/JrSuq75.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
    <br /> <p align="left">
  <li>Change the machines network settings to NatNetwork</li>
    <ul>
      <li>Splunk -> Settings -> Network -> Change "Attached to" from NAT to NAT Network -> OK</li>
    </ul>
      <p align="center">
        <img src="https://i.imgur.com/MPYDyiF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      <br /> <p align="left">
  <li>Setup Splunk Server</li>
     <ul>
        <li>On the diagram I setup the static IP to 192.168.10.0/24</li>
        <li>Setp up a static IP on the Splunk server to reflect the diagram IP.</li>
          <p align="center">
            <img src="https://i.imgur.com/VJAFvgv.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
          <br /> <p align="left">
        <li>Set DHCP from true to no, as we don't want any DHCP as the server requires to have a set IP.</li>
        <li>Add "addresses:[192.168.10.10/24]". This sets the IP address of the server.</li>
        <li>Add "nameserver:   addresses:[8.8.8.8]". This contains the DNS IP that I want to set up.</li>
        <li>Add "routes:   -to: default     via:192.168.10.1" which adds a default route through the gateway.</li>
          <p align="center">
            <img src="https://i.imgur.com/ZDBOZ1y.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
          <br /> <p align="left">
        <li>Configure the changes by running sudo netplan apply.</li>
        <li>Use command "ip a" to check if the IP address has changed to the one I wanted.</li>
        <li>Run ping command on google.com to check if the connection has been established with ip 8.8.8.8 - google ip address.</li>
            <p align="center">
              <img src="https://i.imgur.com/IiZAt7h.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
            <br /> <p align="left">
        <li>Create a splunk account on splunk.com.</li>
        <li>Get the Splunk Enterprise free trial. Select Linux download and download the .deb file.</li>
        <li>Install guest add-ons for virtual box.</li>
              <p align="center">
                <img src="https://i.imgur.com/00uBOGy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
        <li>Click Devices -> Shared Folders -> Shared Folders settings</li>
        <li>Create new folder</li>
        <li>Reboot the machine by typing sudo reboot.</li>
              <p align="center">
                <img src="https://i.imgur.com/SrcEVTs.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
        <li>Add the user to the vbox SF group.</li>
        <li>Type sudo adduser ad vboxsf.</li>
        <li>Type sudo apt-get install virtualbox-guest-utils to install the guest utils.</li>
        <li>Reboot the machine and repeat the process of adding the new user.</li>
        <li>Create a new directory called share by typing mkdir share and mount it to the share directory.</li>
        <li>This has give us access to the folder that is on my machine so I can start to download the splunk enterprise.</li>
              <p align="center">
                <img src="https://i.imgur.com/16H9AMs.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
              <p align="center">
                <img src="https://i.imgur.com/myedkCl.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
        <li>Install Splunk Enterprise.</li>
        <li>Move to the /opt/splunk.</li>
        <li>All the users and groups belong to splunk. This means that it limits the permissions to that user.</li>
        <li>Change into the user splunk by typing sudo -u splunk bash.</li>
              <p align="center">
                <img src="https://i.imgur.com/TVtOigY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
        <li>Move to the directory called bin. There are the binaries files that splunk can use.</li>
        <li>Start Splunk.</li>
              <p align="center">
                <img src="https://i.imgur.com/FlhedMg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
        <li>Run the command "sudo ./splunk enable boot-start -user splunk" to make sure that Splunk starts up every time the virtual machine reboots on user splunk.</li>
              <p align="center">
                <img src="https://i.imgur.com/qNOVnBO.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
              <br /> <p align="left">
     </ul>
  <li>Install Splunk Universal Forwarder and Sysmon on Target Machine and Server</li>
  <ul>
    <li>The installation process is the same on both the target machine and server.</li> <br>
    <li>Rename the windows target machine to target-10 by going into settings -> About - > Rename.</li>
      <p align="center">
          <img src="https://i.imgur.com/iR29LxK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      <br /> <p align="left">
    <li>Change the machine's IP address.</li>
      <p align="center">
          <img src="https://i.imgur.com/iR29LxK.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
      <br /> <p align="left">
    <li></li>
  </ul>
</ol>


<h2>Step 4: Configure Active Directory </h2>
Steps: <br />
<ol>
  <li></li>
</ol>


<h2>Step 5: Generate Telemetry with Kali Linux & ART </h2>

- <b>Kali Linux</b> <br>
- <b>Sysmon</b> <br>
- <b>Install Atomic Red Team</b> <br>
