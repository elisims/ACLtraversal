# ACL Traversal with Bloodhound (Neo4j and Java)

## Installation and Setup
### Installing Java
- I started by downloading the Windows installer for Oracle JDK 11.
- I then used the installer to install Oracle JDK.
- Once the installer had completed, I ran into a huge issue where the version of Java being  used  by  my  computer  was  version  8.  I  needed  to  edit  the  JAVA_HOME environment variable and set it to the JDK folder.
- Once I replaced the folder with the path “C:\Program Files\Java\jdk-11.0.6” the version of Java was working perfectly.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/1.jpg)

### Installing Neo4j
- First I downloaded the neo4j Community Server Edition zip.
- I unzipped the neo4j zip file to the desired location.
- And then I ran the windows terminal app making sure I was running it as administrator. Then I changed directories to the unzipped neo4j folder.
- Navigating to the bin directory in the neo4j folder, I ran the following command:
> - C:\> neo4j.bat install-service
- This installed neo4j as a Windows service. Finally I ran this command:
> - C:\> net start neo4j"

![img](https://github.com/elisims/ACLtraversal/raw/main/images/2.jpg)
![img](https://github.com/elisims/ACLtraversal/raw/main/images/3.jpg)

- Finally, I opened a web browser and navigated to http://localhost:7474/.
- Since this was the first time running neo4j, I needed to authenticate to neo4j in the web console with the username neo4j, and password neo4j.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/4.jpg)

### Installing BloodHound
- First, I downloaded the latest version of the BloodHound GUI.
- I unzipped the folder and double clicked BloodHound.exe.
- Finally, I authenticated with the credentials I set up for neo4j.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/5.jpg)

## Analyzation and Findings
- Almost all networks have a way of distinguishing whether or not an account has admin (in this case  System  Admin)  privilege.  The  user  account  K*****ER  doesn’t  have  the  same separation tag “SA” conjoined onto his name like the rest of the System Admins do.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/6.jpg)

- Pa******o user account (**************YU.EDU) has execute DCOM privileges which can be used to allow code execution under certain conditions by instantiating a COM object on a remote machine and invoking its methods.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/7.jpg)
![img](https://github.com/elisims/ACLtraversal/raw/main/images/8.jpg)

- ******************U.EDU account has sessions on multiple hosts- and is actively being used and logged into hosts that it shouldn’t be. It makes sense for an Admin account to be logged into a domain controller, but shouldn’t be used on other hosts- Because, if any of the hosts that the Admin account has logged into become compromised, the attackers would have a path to DA privileges.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/9.jpg)

- K******R  (research  assistant)  has  excessive  privileges,  as  the  account  is  listed  as  a member of the RESEARCH_ASSISTANTS group, but is also a Domain Admin.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/10.jpg)

- G***************U.EDU is admin to multiple hosts, but LAPS is not installed on V*************YU.EDU.  LAPS  or  Local  Administrator  Password  Solution  is  a  way  for workstation admins to use the Local Admin account on a host. It is free software provided by Microsoft and SHOULD be used in enterprises.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/11.jpg)
![img](https://github.com/elisims/ACLtraversal/raw/main/images/12.jpg)

- K******R has explicit local admin on two machines. This is an issue because when a user is granted explicit admin privileges while also being in other groups (like Domain Admin) if you remove this user from the Domain Admin group, they will remain admin over the hosts they are explicit admins over. By default, administrators have several ways to perform remote code execution on Windows systems, including via RDP, WMI, WinRM, the Service Control Manager, and remote DCOM execution. Further, administrators have several options for impersonating other   users   logged   onto   the   system,   including   plaintext   password   extraction,   token impersonation, and injecting into processes running as another user. So, if this user leaves the company and isn’t removed as local admin explicitly, they would still be able to perform
these tasks even if being removed from the Domain Admin group.

![img](https://github.com/elisims/ACLtraversal/raw/main/images/13.jpg)
