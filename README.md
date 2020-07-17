# MongoDB-bootcamp
## Install docker-compose -- Centos/RedHat
```
# yum install -y docker-compose
```
## MongoDB ReplicaSet docker-compose.yml
```
version: "3"
services:
  mongod01:
    container_name: mongors01
    image: mongo:3.6.13
    command: mongod --replSet rs1 --noprealloc --smallfiles
    ports:
      - "30001:27017"
  mongod02:
    container_name: mongors02
    image: mongo:3.6.13
    command: mongod --replSet rs1 --noprealloc --smallfiles
    ports:
      - "30002:27017"
  mongoa001:
    container_name: mongoa01
    image: mongo:3.6.13
    command: mongod --replSet rs1 --noprealloc --smallfiles
    ports:
      - "30003:27017"
```
## Docker-Compose結果
```
# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
dd1b6e2bf792        mongo:3.6.13        "docker-entrypoint..."   7 seconds ago       Up 5 seconds        0.0.0.0:30003->27017/tcp   mongoa01
c3910e0e617a        mongo:3.6.13        "docker-entrypoint..."   7 seconds ago       Up 5 seconds        0.0.0.0:30001->27017/tcp   mongors01
5f1a8e5df537        mongo:3.6.13        "docker-entrypoint..."   7 seconds ago       Up 5 seconds        0.0.0.0:30002->27017/tcp   mongors02
```
## Mongodb connect
```
# docker exec -it mongors01 mongo
MongoDB shell version v3.6.13
connecting to: mongodb://127.0.0.1:27017/?gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("d06f12ac-bfb3-4996-902e-3f77d3c20f80") }
MongoDB server version: 3.6.13
Welcome to the MongoDB shell.
For interactive help, type "help".
For more comprehensive documentation, see
	http://docs.mongodb.org/
Questions? Try the support group
	http://groups.google.com/group/mongodb-user
Server has startup warnings: 
2019-07-16T06:57:26.668+0000 I CONTROL  [initandlisten] 
2019-07-16T06:57:26.668+0000 I CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2019-07-16T06:57:26.668+0000 I CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
>  
```
# ReplicaSet 
## Replica Set Initiate
```
> rs.initiate()
{
	"info2" : "no configuration specified. Using a default configuration for the set",
	"me" : "c3910e0e617a:27017",
	"ok" : 1,
	"operationTime" : Timestamp(1563260596, 1),
	"$clusterTime" : {
		"clusterTime" : Timestamp(1563260596, 1),
		"signature" : {
			"hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
			"keyId" : NumberLong(0)
		}
	}
rs1:OTHER >
```
## Replica Set, Member Add
```
rs1:PRIMARY> rs.add("mongors02:27017")
{
	"ok" : 1,
	"operationTime" : Timestamp(1563262783, 1),
	"$clusterTime" : {
		"clusterTime" : Timestamp(1563262783, 1),
		"signature" : {
			"hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
			"keyId" : NumberLong(0)
		}
	}
}
```
## Replica Set, Arbitor Add
```
rs1:PRIMARY> rs.addArb("mongoa01:27017")
{
	"ok" : 1,
	"operationTime" : Timestamp(1563262913, 1),
	"$clusterTime" : {
		"clusterTime" : Timestamp(1563262913, 1),
		"signature" : {
			"hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
			"keyId" : NumberLong(0)
		}
	}
}
```
## Replica Set, Final Status
```
rs1:PRIMARY> rs.status()
{
	"set" : "rs1",
	"date" : ISODate("2019-07-16T07:44:56.369Z"),
	"myState" : 1,
	"term" : NumberLong(1),
	"syncingTo" : "",
	"syncSourceHost" : "",
	"syncSourceId" : -1,
	"heartbeatIntervalMillis" : NumberLong(2000),
	"optimes" : {
		"lastCommittedOpTime" : {
			"ts" : Timestamp(1563263088, 1),
			"t" : NumberLong(1)
		},
		"readConcernMajorityOpTime" : {
			"ts" : Timestamp(1563263088, 1),
			"t" : NumberLong(1)
		},
		"appliedOpTime" : {
			"ts" : Timestamp(1563263088, 1),
			"t" : NumberLong(1)
		},
		"durableOpTime" : {
			"ts" : Timestamp(1563263088, 1),
			"t" : NumberLong(1)
		}
	},
	"members" : [
		{
			"_id" : 0,
			"name" : "c3910e0e617a:27017",
			"health" : 1,
			"state" : 1,
			"stateStr" : "PRIMARY",
			"uptime" : 2851,
			"optime" : {
				"ts" : Timestamp(1563263088, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2019-07-16T07:44:48Z"),
			"syncingTo" : "",
			"syncSourceHost" : "",
			"syncSourceId" : -1,
			"infoMessage" : "",
			"electionTime" : Timestamp(1563260596, 2),
			"electionDate" : ISODate("2019-07-16T07:03:16Z"),
			"configVersion" : 3,
			"self" : true,
			"lastHeartbeatMessage" : ""
		},
		{
			"_id" : 1,
			"name" : "mongors02:27017",
			"health" : 1,
			"state" : 2,
			"stateStr" : "SECONDARY",
			"uptime" : 312,
			"optime" : {
				"ts" : Timestamp(1563263088, 1),
				"t" : NumberLong(1)
			},
			"optimeDurable" : {
				"ts" : Timestamp(1563263088, 1),
				"t" : NumberLong(1)
			},
			"optimeDate" : ISODate("2019-07-16T07:44:48Z"),
			"optimeDurableDate" : ISODate("2019-07-16T07:44:48Z"),
			"lastHeartbeat" : ISODate("2019-07-16T07:44:55.682Z"),
			"lastHeartbeatRecv" : ISODate("2019-07-16T07:44:55.725Z"),
			"pingMs" : NumberLong(0),
			"lastHeartbeatMessage" : "",
			"syncingTo" : "c3910e0e617a:27017",
			"syncSourceHost" : "c3910e0e617a:27017",
			"syncSourceId" : 0,
			"infoMessage" : "",
			"configVersion" : 3
		},
		{
			"_id" : 2,
			"name" : "mongoa01:27017",
			"health" : 1,
			"state" : 7,
			"stateStr" : "ARBITER",
			"uptime" : 182,
			"lastHeartbeat" : ISODate("2019-07-16T07:44:55.682Z"),
			"lastHeartbeatRecv" : ISODate("2019-07-16T07:44:55.713Z"),
			"pingMs" : NumberLong(0),
			"lastHeartbeatMessage" : "",
			"syncingTo" : "",
			"syncSourceHost" : "",
			"syncSourceId" : -1,
			"infoMessage" : "",
			"configVersion" : 3
		}
	],
	"ok" : 1,
	"operationTime" : Timestamp(1563263088, 1),
	"$clusterTime" : {
		"clusterTime" : Timestamp(1563263088, 1),
		"signature" : {
			"hash" : BinData(0,"AAAAAAAAAAAAAAAAAAAAAAAAAAA="),
			"keyId" : NumberLong(0)
		}
	}
}
```
