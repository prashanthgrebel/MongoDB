MongoDB



############################################################################################### mongDB Basic Commands #######################################################################################################################


MongoDB is a source-available cross-platform document-oriented database program. Classified as a NoSQL database program, MongoDB uses JSON-like documents with optional schemas.

Mongo Basic Commands
Note: Run Below Commands from Admin database inside Mongo

1. Connect to Mongo
Command:
```
 mongo --host <Server-IP> --username <User-name> --password <Password> --authenticationDatabase <Authentication Database>
Example:
mongo --host 192.168.39.20 --username prashanthg --password prashanthg@123 --authenticationDatabase admin
```
2. List Databases
Command:
```
show dbs
```
3. Enter database or Create database
Command:
```
use <database-Name> (If database is not there it will be created)
Example:
use prashanthg
```
4. List collections (tables) in database
Command:
```use <database-Name>
show collections or show tables
```
5. To confirm data is present in a table (After going inside database with "use" command)
Command:
```
db.<table-Name>.find().pretty().limit(1)
Example:
db.prashanthg.find().pretty().limit(1)
```
6. To See total count of data inside mongo collection.
Command:
```
db.<Table-Name>.count()
Example:
db.prashanthg.count()
```
8. To List All the Users
Command:
```
db.getUsers()
```
10. To List particular user
Command:
```
db.getUser('<User-Name>')
```
12. To List particular User with Restrictions
Command:
```
db.getUser( "<User-Name>", { showAuthenticationRestrictions: true} )
```
14. To Create User
 i. Create User With Inbuilt Access (Accessible from All IPs)
Command:
```
db.createUser({user: "<User-Name>", pwd: "<Password>", roles: [ "<Access-Type>" ]})
Example:
db.createUser({user: "prashanthg", pwd: "c0mp_ap!", roles: [ "readWriteAnyDatabase" ]})
```
  ii. Create User with access on Particular Databases (Accessible from All IPs)
Command:
```
db.createUser({user: "<User-Name>", pwd: "<Password>", roles:[{role: "<AccessType>", db: "<Database-Name>"}]})
Example:
Single Database
===============
db.createUser({user: "prashanthg", pwd: "prashanthg", roles: [ { role: "readWrite", db: "db_refer_earn" } ]
Multiple Database
=================
db.createUser(
{
user: "prashanthg",
pwd: "prashanthg",
roles: [
{ role: "read", db: "reporting" },
{ role: "read", db: "products" },
{ role: "read", db: "sales" },
{ role: "readWrite", db: "accounts" }
]
}
)
```
 iii. Create User With Access From limtited Servers (Accessible only from servers mentioned in Client Source)
Command:
```
db.createUser({user: "<User Name>", pwd: "<Password>", roles: [ "readWriteAnyDatabase" ], authenticationRestrictions:[{ clientSource: ["<ip1>","<ip2>","<ip3>",.....,"<ipn>"]}]})
Example:
db.createUser({user: "prashanthg", pwd: "prashanthg123", roles: [ "readWriteAnyDatabase" ], authenticationRestrictions:[{ clientSource: ["192.168.39.71","192.168.39.72","192.168.17.222","192.168.17.223"]}]})
```

11. Update Users
Note: Updating User in Mongo is an update process i.e New data over-writes old data,
therefore, Addition and deletion of particular access should be updated with all the access required and not just the data to be updated
Command:
db.updateUser( "<User-Name>",
{
roles : [
{ role : "<Access-Type>", db : "<Databases>" }
],
authenticationRestrictions: [
{ clientSource: ["<ip1>","<ip2>","<ip3>",.....,"<ipn>"] }
]
} )
Example:
A User Created with Below commnds
db.createUser({user: "prashanthg", pwd: "c0mp_ap!", roles: [ "readWriteAnyDatabase" ], authenticationRestrictions:[{ clientSource: ["192.168.39.71","192.168.39.72","192.168.17.222","192.168.17.223"]}]})
If Now we have to give access to 192.168.13.138 also then Run below Command
db.updateUser( "prashanthg",
{
roles : [
{ role : "readAnyDatabase", db : "admin" }
],
authenticationRestrictions: [
{ clientSource: ["192.168.39.71","192.168.39.72","192.168.17.222","192.168.17.223","192.168.13.138"] }
]
} )


12. Update User Password
Command:
db.changeUserPassword("<Username>", "<New-Password>")
Example:
db.changeUserPassword("prashanthg", "prashanthg123")


13. Delete User
Command:
db.dropUser("<User-Name>")
Example:
db.dropUser("prashanthg")


14. Execute mongo Commands from linux bash terminal
Command:
mongo --host <Server-IP> --username <User-name> --password <Password> --authenticationDatabase <Authentication Database> --eval "printjson(<mongo-command>)"
Example:
mongo --host 192.168.39.20 --username prashanthg --password prashanthg@123 --authenticationDatabase admin --eval "printjson(db.serverStatus())"
mongo --quiet --host 192.168.39.20 --username prashanthg --password prashanthg@123 --authenticationDatabase admin --eval "printjson(db.getMongo().getDBNames())"


################################################################################### Mongo Clustering ########################################################################################################################3


Installation on centos 7

Step 1
[root@1-102 bin]# cat /etc/yum.repos.d/mongodb.repo
[MongoDB]
name=MongoDB Repository
baseurl=http://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
[root@1-102 bin]# 

Step2
sudo yum install mongodb-org -y
systemctl start mongod.service



Replication setup:

  >> What is MongoDB Replica?
     Replica sets are basically multiple mongod processes running on multiple servers that maintain the same data set. The replica set can have a minimum of three nodes. 
     These multiple nodes in a replica set play different roles and all together forming one replica set.
     Roles in the replica set
        Primary
        Secondary
        Arbiter

>> Primary node: It is the master server of the replica set and all the read and write operations will be performed on it.

>> Secondary node: Thes secondary node will sync with the primary node and thus maintains the copy of the same data set of the primary. 
   There can be multiple secondary nodes in one replica set. While the unavailability of the primary node it will start an election between the available secondary nodes and out of these nodes 
   one will become the next primary node of this replica set and continues the normal operations, thus providing high data availability to the database clients.
   
>> Arbiter: Most Importantly this will not hold any copies of data, only votes in elections to decide which node will be the next primary.
   Commonly used in infrastructures where multiple secondary nodes are contesting to become the next primary, this will help in reducing the election time.

###########################################################################################################################

Replication setup
Disable/comment below block block in config file on each server and start service


#security:
#  keyFile: /etc/mongo/jdmongo.key
#  authorization: enabled
#
#
##operationProfiling:
#
#replication:
#  replSetName: "configReplSet"

login to mongo with localhost add/create  user with role root  and required users if any..

#mongo --host localhost --port 27018
use admin 
db.createUser(
  {
	user: 'svradmin',
	pwd: 'secureSvradmin',
	roles: [ { role: 'root', db: 'admin' } ]
  }
);

 Revert back changes made in config file and restart service on each host
 test login 
 # mongo --host shardsvr1 -u svradmin -p secureSvradmin --port 27018 --authenticationDatabase admin
 == Repeat for all hosts ==
 
 
  Once configReplSet port is started on all hosts login to any server and create cluster
 cluster

 # mongo --host shardsvr1 -u svradmin -p secureSvradmin --port 27018 --authenticationDatabase admin
 
 use admin
 
 
rs.initiate(
   {
      _id: "configReplSet",
      version: 1,
      members: [
         { _id: 0, host : "configsvr1:27018" },
         { _id: 1, host : "configsvr2:27018" },
         { _id: 2, host : "configsvr3:27018" }
      ]
   }
)

######################################################################################################################################
