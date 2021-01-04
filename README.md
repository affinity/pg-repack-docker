# pg-repack-docker

Docker image for 'pg_repack' (PostgreSQL extension) to use 'client-side' to run/invoke the actual repack functionality 
for a PostgreSQL database with the extension installed.

Reference: https://github.com/reorg/pg_repack

This has been modified to use the version of postgres we're using in prod and to match the version of pg_repack available on RDS.


### build

    docker build . -t pg-repack:1.4.5

### push to ECR

    TAG_VERSION=1.4.5
    aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 798215447140.dkr.ecr.us-west-2.amazonaws.com
    docker tag pg_repack:$TAG_VERSION 798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:$TAG_VERSION
    docker push 798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:$TAG_VERSION
    
### run (example)
    
    docker run -e PGPASSWORD=secret -it --rm 798215447140.dkr.ecr.us-west-2.amazonaws.com/pg_repack:1.4.5 pg_repack -h localhost -p 5430 -U affinity --dbname=affinity --dry-run --no-superuser-check

