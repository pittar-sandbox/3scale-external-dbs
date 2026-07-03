# 3Scale 2.16+ with External Databases

## Introduction

As of 3Scale 2.16, the main RDBMS as well as the cache (Valkey) no longer have an "operator managed" option.  This means those databases need to be configured before deploying the `APIManager` resource.

## Fresh Install

The 3Scale docs don't show how to deploy a database, so it's up to the end user to figure this part out.  Most customers will probably have a managed database service such as *Azure Database for PostgreSQL* or *AWS PostgreSQL RDS*, but if you are doing a demo or proof of concept, it's easier to just spin up a database container.

Similarly, most customers rely on managed Redis services.  This will be a probelm in 3Scale 2.17... but for now customers can use *AWS ElastiCache*. I'm not sure what the Azure alternative is for a managed Redis instance that supports Redis 7.2.

In a pilot or demo situaiton, it's faster and easier to use a container for this.  The [3Scale Docs show most of what is required to deploy Valkey](https://docs.redhat.com/en/documentation/red_hat_3scale_api_management/2.16/html-single/installing_red_hat_3scale_api_management/index#backend_valkey_resources), but only in the context of migrating from Redis.  It doesn't show what to do for a clean install (no migration).  You can figure it out by reading through the full migration guide, skipping the data export/import steps, and starting with the final Valkey configuration.

I've created all three required databases (1 x Postgresql, 2 x Valkey) in this repo to make spinning up a fresh 3Scale 2.16 install easier for a demo or pilot.

## Other Caveats

This demo is meant to run in AWS where a `efs-csi` storage class is already configured.  This is required for system storage if you don't have easy access to create an object storage (S3) bucket.

## Setup

1. Install the 3Scale operator.  I've left this part out of this repo, but I just install the `Red Hat Integration - 3Scale` operator into a new `3Scale` namespace for simplicity.
2. Create the databases:
  * `oc apply -k manifests/databases/postgresql`
  * `oc apply -k manifests/databases/valkey`
3. Once all there databases are deployed and healthy, update the `apimanager.yaml` file with your wildcard domain, then run:
  * `oc apply -k manifests/apimanager`

Now go get a coffee... this will take a few minutes.

## Next Steps

Find the default user/pass for both the default "admin" portal and "master" portal, sign in, and have fun!