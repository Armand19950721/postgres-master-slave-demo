# postgres-master-slave-demo
## Reference
https://medium.com/swlh/postgresql-replication-with-docker-c6a904becf77
## Startup
### Run the Master
1. Copy Configuration Files
2. Copy postgres.conf and pg_hba.conf to ./data/psdata.
3. Restart the Master
4. Ensure there are no errors upon restarting.
5. Execute SQL Commands
```
CREATE USER replicator WITH REPLICATION ENCRYPTED PASSWORD 'your user pwd';
SELECT * FROM pg_create_physical_replication_slot('replication_slot_slave1');
SELECT * FROM pg_replication_slots;
```
### Enter the Container and Execute Commands
```
docker exec -it meta_commerce_master_db bash
pg_basebackup -D /tmp/postgresslave -S replication_slot_slave1 -X stream -P -U replicator -Fp -R
```
1. Replace postgresql.auto.conf
2. Copy Backup to Slave
3. Copy ./postgresslave from the tmp directory to the slave's data folder.
4. Delete Data in pg_wal
5. Run the Slave
### Testing
```
DROP TABLE accounts;

CREATE TABLE accounts (
  user_id serial PRIMARY KEY,
  username VARCHAR (50) NOT NULL
);

INSERT INTO accounts(username)
VALUES ('armand');

SELECT * FROM accounts;
```
### Backup

#### Master
```
cp pg_hba.conf data/pgdata/pg_hba.conf
cp postgresql.conf data/pgdata/postgresql.conf
cp -r /home/ubuntu/code/METAR_backend_db/master/data/tmp/postgresslave/ /home/ubuntu/code/METAR_backend_db/slave/postgresslave
```

#### Slave
```
mkdir data
cp -r postgresslave data/postgresslave
cp ../master/postgresql.auto.conf data/postgresslave/postgresql.auto.conf
```