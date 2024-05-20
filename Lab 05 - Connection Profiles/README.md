<h1>Lab 5 - Connection Profiles</h1>
In this section we will discuss:

1. What are connection profiles?
2. How to create a connection profile
3. How to use secrets in a connection profiles

**Step 1 - What are connection profiles?**

Connection profiles are used to define access methods and security credentials for a specific application. They can be referenced by multiple jobs. To do this, you must deploy the connection profile definition before running the relevant jobs.

**Step 2 - How to create a Connection Profile**
Defining connection profiles are simple and can be done from both the AAPI and the GUI if you have the right level of access.

Below is a simple connection profile definition for a File Transfer via FTP.

```
{
 "FTPConn" : {
   "Type" : "ConnectionProfile:FileTransfer:FTP",
   "TargetAgent" : "AgentHost",
   "TargetCTM" : "CTMHost",
   "HostName": "FTPServer",
   "Port": "21",
   "User" : "FTPUser",
   "Password" : "ftp password",
 }
}
```
As it can be seen the connection profile is unsecure as the username and password are visable in the definition. This will pose a security risk and therefore needs to have some method of encryption in order to secure the definition.

**Step 3 - How to use secrets in connection profiles**
You can use the Secret object in your JSON code when you do not want to expose confidential information in the source (for example, the password field in a Connection Profile).

To define a secret, use the following syntax:
```
ctm config secret::add <name> <value>
```
For example:
```
ctm config secret::add FTPServerUsername FTPUser
ctm config secret::add FTPUserPassword "ftp password"
```
The username and password for the example connection profile will now be stored encrypted in the Control-M database.

Now lets review the updated connection profile:
```
{
 "FTPConn" : {
   "Type" : "ConnectionProfile:FileTransfer:FTP",
   "TargetAgent" : "AgentHost",
   "TargetCTM" : "CTMHost",
   "HostName": "FTPServer",
   "Port": "21",
   "User" : {"Secret": "FTPServerUsername"},
   "Password" : {"Secret": "FTPUserPassword"},
 }
}
```
As it can be seen, the username and password are now not exposed.
To list what the names of the secrets are, use the following command:
```
ctm config secrets::get
```
Here are some examples of a different Connection Profiles:
```
{
  "INFORMATICA_CONNECTION": {
    "Type": "ConnectionProfile:Informatica",
    "TargetAgent": "AgentHost",
    "TargetCTM": "CTMHost",
    "Host": "InformaticaHost",
    "Port": "7333",
    "User": "UserName",
    "Password": "Password",
    "PowerCenterDomain": "DomainName",
    "Repository": "RepositoryName",
    "IntegrationService": "ServiceName",
    "SecurityDomain": "Native",
    "ConnectionType": "HTTP",
    "MaxConcurrentConnections": "100",
    "Centralized": false
  }
}

{
  "ORACLE_CONNECTION_PROFILE": {   
	"Type": "ConnectionProfile:Database:Oracle:SID",   
	"TargetCTM": "controlm",   
	"Port": "1521",   
	"TargetAgent": "AgentHost",
	"Host": "OracleHost",
	"User": "db user",   
	"Password": "db password",
	"SID": "ORCL" 
  }
}

{
  "ORACLE_CONNECTION_PROFILE": {   
	"Type": "ConnectionProfile:Database:Oracle:ServiceName",   
	"TargetCTM": "controlm",   
	"Port": "1521",   
	"TargetAgent": "AgentHost",
	"Host": "OracleHost",
	"User": "db user",   
	"Password": "db password",
	"ServiceName": "ORCL" 
  }
}

```
This now concludes the section on connection profiles.

There will now be a Q&A session.