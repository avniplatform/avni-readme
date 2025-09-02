---
title: Copy database from prod to staging for testing
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
```shell
# Copying database from prod to staging for testing.
# these steps cannot be executed as a single bash script.
# make sure you have ssh configuration setup and you are able to ssh into envs.

# ssh into staging box
ssh staging-openchs
# stop server
service openchs stop

# in a different bash session setup tunnel
ssh -T -L 3333:stagingdb.openchs.org:5432 staging-openchs
# in a different bash session connect to staging db
psql -Uopenchs -d openchs -h localhost -p 3333

# drop schema, effectively deleting everything but the database
DROP SCHEMA public CASCADE;
CREATE SCHEMA public;
# reset the schema
GRANT ALL ON SCHEMA public TO postgres;
GRANT ALL ON SCHEMA public TO public;
\q;

# in a different bash session login to prod
ssh prod-openchs
# within ssh shell run pg_dump to get prod db.dump
pg_dump -Uopenchs -h serverdb.openchs.org -d openchs > prod-database-dump.sql

# in a different bash session download database dump from prod box to local
scp prod-openchs:~/prod-database-dump.sql ~/prod-database-dump.sql

# apply dump to staging db
psql -Uopenchs -d openchs -h localhost -p 3333 -f ~/prod-database-dump.sql
# once it is complete, start the staging server in staging box
service openchs start
# once it is started and no problem kill the ssh tunnel and delete the temporary dump file from prod box
# exit all the sessions
```
