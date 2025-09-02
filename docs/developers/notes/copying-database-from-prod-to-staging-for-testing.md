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
[block:code]
{
  "codes": [
    {
      "code": "# Copying database from prod to staging for testing.\n# these steps cannot be executed as a single bash script.\n# make sure you have ssh configuration setup and you are able to ssh into envs.\n\n# ssh into staging box\nssh staging-openchs\n# stop server\nservice openchs stop\n\n# in a different bash session setup tunnel\nssh -T -L 3333:stagingdb.openchs.org:5432 staging-openchs\n# in a different bash session connect to staging db\npsql -Uopenchs -d openchs -h localhost -p 3333\n\n# drop schema, effectively deleting everything but the database\nDROP SCHEMA public CASCADE;\nCREATE SCHEMA public;\n# reset the schema\nGRANT ALL ON SCHEMA public TO postgres;\nGRANT ALL ON SCHEMA public TO public;\n\\q;\n\n# in a different bash session login to prod\nssh prod-openchs\n# within ssh shell run pg_dump to get prod db.dump\npg_dump -Uopenchs -h serverdb.openchs.org -d openchs > prod-database-dump.sql\n\n# in a different bash session download database dump from prod box to local\nscp prod-openchs:~/prod-database-dump.sql ~/prod-database-dump.sql\n\n# apply dump to staging db\npsql -Uopenchs -d openchs -h localhost -p 3333 -f ~/prod-database-dump.sql\n# once it is complete, start the staging server in staging box\nservice openchs start\n# once it is started and no problem kill the ssh tunnel and delete the temporary dump file from prod box\n# exit all the sessions\n",
      "language": "shell"
    }
  ]
}
[/block]