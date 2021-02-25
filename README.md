# pg-repack-docker

Docker image for 'pg_repack' (PostgreSQL extension) to use 'client-side' to run/invoke the actual repack functionality 
for a PostgreSQL database with the extension installed.

Reference: https://github.com/reorg/pg_repack

This has been modified to use the version of postgres we're using in prod and to match the version of pg_repack available on RDS.

The following commands depend on having the version you want to use and tag in the following env var:

    TAG_VERSION=1.4.5

### Build

    docker build . -t pg-repack:$TAG_VERSION

### Push to ECR

    aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 798215447140.dkr.ecr.us-west-2.amazonaws.com
    docker tag pg_repack:$TAG_VERSION 798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:$TAG_VERSION
    docker push 798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:$TAG_VERSION
    
### Run
    
    # All tables:
    
    kubectl run pgrepack --env="PGPASSWORD=<password>" --image=798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:1.4.5 --restart=Never pg_repack -- -h <db-url> -p 5432 -U affinity --dbname=affinity --no-superuser-check -j 4

    # single table
    
    kubectl run pgrepack --env="PGPASSWORD=<password>" --image=798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:1.4.5 --restart=Never pg_repack -- -h <db-url> -p 5432 -U affinity --dbname=affinity --no-superuser-check -j 4 --table <tableName>    
    
### Monitoring

You can follow progress of the repack using `kubectl logs -f pgrepack`
If you need to stop the repack for whatever reason (space is getting too low for example), you can kill the pod using `kubectl delete pod pgrepack` and it will ususally clean up after itself. If it didn't, you can cleanup by removing the pg_repack extension and then reinstalling, that forces it to clean itself up.
