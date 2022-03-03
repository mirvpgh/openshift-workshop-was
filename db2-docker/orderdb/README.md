# Set up Db2 database "cos-orderdb" for the cos application running in WAS Tradional VM. The database will run in Docker on the local Desktop VM in the environment. 


## Clone the workshop git repo
```
git clone https://github.com/IBMTechSales/openshift-workshop-was
```

## Change to the db2-docker directory
```
cd /home/ibmuser/openshift-workshop-was/db2-docker/orderdb
```


## Build the database image 
Using the Dockerfile in the current directory. It copies all of the scripts that run when the docker run command executes.
```
docker build . -t cos-orderdb
```


## Create the docker container 
The command creates the ORDERDB database, sets the db2inst1 password, and the scripts that were copied to the /var/customm folder on the image automatically run (when using the IBM DB2 11 Docker image. The scripts create the tables and populate the DB. It also stores the DATA on the local VM in the locateion specified by the -v option. And it maps to the /database directory in the docker container. Add network for cos app. Add hostname so cos app can access db.
```
docker run -itd --name cos-orderdb --privileged --network=cosNetwork --hostname=orderdb -p 50000:50000 -e DBNAME=ORDERDB -e LICENSE=accept -e DB2INST1_PASSWORD=db2inst1 -v /home/ibmuser/orderdb/database:/database  cos-orderdb:latest
```

## Repeat checking of the db2 logs to ensure DB is setup and running
Note: The .sql files and the .sh files run. The .swl files show errors, but the PopulateDB.sh script runs and successfully creates the table and populates the DB by using the same .sql files. 
```
docker logs cos-orderdb
```

### FYI: The docker run command invokes the populateDB.sh inside of the container
I added the following commands to set the db2inst1 user password to never expire. Run docker logs cos-orderdb command to see the output that it was successful. 
```
echo "set password for db2inst1 to never expire"
passwd -x 9999 db2inst1
```

## Start and stop the db2 container as needed 

### Start DB2
```
docker start cos-orderdb
```

### Stop DB2
```
docker stop cos-orderdb
```


## Inside the docker container, commands to verify all is OK 
```
docker exec -it cos-orderdb /bin/sh
```
### Inside the container 
```
su db2inst1
db2 list database directory
db2 connect to orderdb
db2 list tables
```
### check data
```
db2    
select * product
quit
```

## check the /database directory at the root of the filesystem. 
The Data folder should have the database content. 
```
cd /database
```




