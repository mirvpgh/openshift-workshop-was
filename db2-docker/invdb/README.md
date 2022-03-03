# setup Db2 database "cos-orderdb" for the cos application running in WAS Tradional VM. The database will runn in Docker on th elocal Desktop VM in the environment. 


## Clone the workshop git repo
```
git clone https://github.com/IBMTechSales/openshift-workshop-was
```

## Change to the db2-docker directory
```
cd /home/ibmuser/openshift-workshop-was/db2-docker/invdb
```


## Build the database image using the Dockerfile in the current directory
It copies all of the scripts that will run when the docker run command is executes 
```
docker build . -t cos-invdb
```

## Create the docker container. 
The command creates the ORDERDB database, sets the db2inst1 password, and the scripts that were copied to the /var/customm folder on the image automatically run (when using the IBM DB2 11 Docker image. The scripts create the tables and populate the DB. It also stores the DATA on the local VM in the locateion specified by the -v option. And it maps to the /database directory in the docker container. 
```
docker run -itd --name cos-invdb --privileged -p 55000:50000 -e DBNAME=INVDB -e LICENSE=accept -e DB2INST1_PASSWORD=db2inst1 -v /home/ibmuser/invdb/database:/database  cos-invdb:latest
```

## Repeat checking of the db2 logs to ensure DB is setup and running
Note: The .sql file sand the .sh files get executed. The .swl files will show errors, but the PopulateDB.sh script should run, and successfull create the table and populate the DB, using the same .sql files. 
```
docker logs cos-invdb
```


## Start and stop the db2 container as needed 

### Start DB2
```
docker start cos-invdb
```

### Stop DB2
```
docker stop cos-invdb
```


## Inside the docker container, commands to verify all is OK 
```
docker exec -it cos-invdb /bin/sh
```
### Inside the container 
```
su db2inst1
db2 list database directory
db2 connect to invdb
db2 list tables
```
### Check data
```
db2 
select * from product
select * from supplier
quit
```

## Check the /database dorectory at the root of the filesystem. The Data folder should have the database content. 

cd /database












