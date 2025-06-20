# dokku dolt [![Build Status](https://img.shields.io/github/actions/workflow/status/bixoto/dokku-dolt/ci.yml?branch=master&style=flat-square "Build Status")](https://github.com/bixoto/dokku-dolt/actions/workflows/ci.yml?query=branch%3Amaster)

Unofficial dolt plugin for dokku. Currently defaults to installing [dolthub/dolt-sql-server 1.55.2](https://hub.docker.com/r/dolthub/dolt-sql-server/).

**WORK IN PROGRESS -- NOT READY FOR PRODUCTION**

## Requirements

- dokku 0.19.x+
- docker 1.8.x

## Installation

```shell
# on 0.19.x+
sudo dokku plugin:install https://github.com/bixoto/dokku-dolt.git --name dolt
```

## Commands

```
dolt:app-links <app>                               # list all dolt service links for a given app
dolt:backup <service> <bucket-name> [--use-iam]    # create a backup of the dolt service to an existing s3 bucket
dolt:backup-auth <service> <aws-access-key-id> <aws-secret-access-key> <aws-default-region> <aws-signature-version> <endpoint-url> # set up authentication for backups on the dolt service
dolt:backup-deauth <service>                       # remove backup authentication for the dolt service
dolt:backup-schedule <service> <schedule> <bucket-name> [--use-iam] # schedule a backup of the dolt service
dolt:backup-schedule-cat <service>                 # cat the contents of the configured backup cronfile for the service
dolt:backup-set-encryption <service> <passphrase>  # set encryption for all future backups of dolt service
dolt:backup-set-public-key-encryption <service> <public-key-id> # set GPG Public Key encryption for all future backups of dolt service
dolt:backup-unschedule <service>                   # unschedule the backup of the dolt service
dolt:backup-unset-encryption <service>             # unset encryption for future backups of the dolt service
dolt:backup-unset-public-key-encryption <service>  # unset GPG Public Key encryption for future backups of the dolt service
dolt:clone <service> <new-service> [--clone-flags...] # create container <new-name> then copy data from <name> into <new-name>
dolt:create <service> [--create-flags...]          # create a dolt service
dolt:destroy <service> [-f|--force]                # delete the dolt service/data/container if there are no links left
dolt:enter <service>                               # enter or run a command in a running dolt service container
dolt:exists <service>                              # check if the dolt service exists
dolt:export <service>                              # export a dump of the dolt service database
dolt:expose <service> <ports...>                   # expose a dolt service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)
dolt:import <service>                              # import a dump into the dolt service database
dolt:info <service> [--single-info-flag]           # print the service information
dolt:link <service> <app> [--link-flags...]        # link the dolt service to the app
dolt:linked <service> <app>                        # check if the dolt service is linked to an app
dolt:links <service>                               # list all apps linked to the dolt service
dolt:list                                          # list all dolt services
dolt:logs <service> [-t|--tail] <tail-num-optional> # print the most recent log(s) for this service
dolt:pause <service>                               # pause a running dolt service
dolt:promote <service> <app>                       # promote service <service> as DATABASE_URL in <app>
dolt:restart <service>                             # graceful shutdown and restart of the dolt service container
dolt:set <service> <key> <value>                   # set or clear a property for a service
dolt:start <service>                               # start a previously stopped dolt service
dolt:stop <service>                                # stop a running dolt service
dolt:unexpose <service>                            # unexpose a previously exposed dolt service
dolt:unlink <service> <app>                        # unlink the dolt service from the app
dolt:upgrade <service> [--upgrade-flags...]        # upgrade service <service> to the specified versions
```

## Usage

Help for any commands can be displayed by specifying the command as an argument to dolt:help. Plugin help output in conjunction with any files in the `docs/` folder is used to generate the plugin documentation. Please consult the `dolt:help` command for any undocumented commands.

### Basic Usage

### create a dolt service

```shell
# usage
dokku dolt:create <service> [--create-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-m|--memory MEMORY`: container memory limit in megabytes (default: unlimited)
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-p|--password PASSWORD`: override the user-level service password
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-r|--root-password PASSWORD`: override the root-level service password
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for dolt docker container

Create a dolt service named lollipop:

```shell
dokku dolt:create lollipop
```

You can also specify the image and image version to use for the service. It *must* be compatible with the dolthub/dolt-sql-server image.

```shell
export DOLT_IMAGE="dolthub/dolt-sql-server"
export DOLT_IMAGE_VERSION="${PLUGIN_IMAGE_VERSION}"
dokku dolt:create lollipop
```

You can also specify custom environment variables to start the dolt service in semicolon-separated form.

```shell
export DOLT_CUSTOM_ENV="USER=alpha;HOST=beta"
dokku dolt:create lollipop
```

### print the service information

```shell
# usage
dokku dolt:info <service> [--single-info-flag]
```

flags:

- `--config-dir`: show the service configuration directory
- `--data-dir`: show the service data directory
- `--dsn`: show the service DSN
- `--exposed-ports`: show service exposed ports
- `--id`: show the service container id
- `--internal-ip`: show the service internal ip
- `--initial-network`: show the initial network being connected to
- `--links`: show the service app links
- `--post-create-network`: show the networks to attach to after service container creation
- `--post-start-network`: show the networks to attach to after service container start
- `--service-root`: show the service root directory
- `--status`: show the service running status
- `--version`: show the service image version

Get connection information as follows:

```shell
dokku dolt:info lollipop
```

You can also retrieve a specific piece of service info via flags:

```shell
dokku dolt:info lollipop --config-dir
dokku dolt:info lollipop --data-dir
dokku dolt:info lollipop --dsn
dokku dolt:info lollipop --exposed-ports
dokku dolt:info lollipop --id
dokku dolt:info lollipop --internal-ip
dokku dolt:info lollipop --initial-network
dokku dolt:info lollipop --links
dokku dolt:info lollipop --post-create-network
dokku dolt:info lollipop --post-start-network
dokku dolt:info lollipop --service-root
dokku dolt:info lollipop --status
dokku dolt:info lollipop --version
```

### list all dolt services

```shell
# usage
dokku dolt:list
```

List all services:

```shell
dokku dolt:list
```

### print the most recent log(s) for this service

```shell
# usage
dokku dolt:logs <service> [-t|--tail] <tail-num-optional>
```

flags:

- `-t|--tail [<tail-num>]`: do not stop when end of the logs are reached and wait for additional output

You can tail logs for a particular service:

```shell
dokku dolt:logs lollipop
```

By default, logs will not be tailed, but you can do this with the --tail flag:

```shell
dokku dolt:logs lollipop --tail
```

The default tail setting is to show all logs, but an initial count can also be specified:

```shell
dokku dolt:logs lollipop --tail 5
```

### link the dolt service to the app

```shell
# usage
dokku dolt:link <service> <app> [--link-flags...]
```

flags:

- `-a|--alias "BLUE_DATABASE"`: an alternative alias to use for linking to an app via environment variable
- `-q|--querystring "pool=5"`: ampersand delimited querystring arguments to append to the service link
- `-n|--no-restart "false"`: whether or not to restart the app on link (default: true)

A dolt service can be linked to a container. This will use native docker links via the docker-options plugin. Here we link it to our `playground` app.

> NOTE: this will restart your app

```shell
dokku dolt:link lollipop playground
```

The following environment variables will be set automatically by docker (not on the app itself, so they won’t be listed when calling dokku config):

```
DOKKU_DOLT_LOLLIPOP_NAME=/lollipop/DATABASE
DOKKU_DOLT_LOLLIPOP_PORT=tcp://172.17.0.1:3306
DOKKU_DOLT_LOLLIPOP_PORT_3306_TCP=tcp://172.17.0.1:3306
DOKKU_DOLT_LOLLIPOP_PORT_3306_TCP_PROTO=tcp
DOKKU_DOLT_LOLLIPOP_PORT_3306_TCP_PORT=3306
DOKKU_DOLT_LOLLIPOP_PORT_3306_TCP_ADDR=172.17.0.1
```

The following will be set on the linked application by default:

```
DATABASE_URL=mysql://mysql:SOME_PASSWORD@dokku-dolt-lollipop:3306/lollipop
```

The host exposed here only works internally in docker containers. If you want your container to be reachable from outside, you should use the `expose` subcommand. Another service can be linked to your app:

```shell
dokku dolt:link other_service playground
```

It is possible to change the protocol for `DATABASE_URL` by setting the environment variable `DOLT_DATABASE_SCHEME` on the app. Doing so will after linking will cause the plugin to think the service is not linked, and we advise you to unlink before proceeding.

```shell
dokku config:set playground DOLT_DATABASE_SCHEME=mysql2
dokku dolt:link lollipop playground
```

This will cause `DATABASE_URL` to be set as:

```
mysql2://mysql:SOME_PASSWORD@dokku-dolt-lollipop:3306/lollipop
```

### unlink the dolt service from the app

```shell
# usage
dokku dolt:unlink <service> <app>
```

flags:

- `-n|--no-restart "false"`: whether or not to restart the app on unlink (default: true)

You can unlink a dolt service:

> NOTE: this will restart your app and unset related environment variables

```shell
dokku dolt:unlink lollipop playground
```

### set or clear a property for a service

```shell
# usage
dokku dolt:set <service> <key> <value>
```

Set the network to attach after the service container is started:

```shell
dokku dolt:set lollipop post-create-network custom-network
```

Set multiple networks:

```shell
dokku dolt:set lollipop post-create-network custom-network,other-network
```

Unset the post-create-network value:

```shell
dokku dolt:set lollipop post-create-network
```

### Service Lifecycle

The lifecycle of each service can be managed through the following commands:

### enter or run a command in a running dolt service container

```shell
# usage
dokku dolt:enter <service>
```

A bash prompt can be opened against a running service. Filesystem changes will not be saved to disk.

> NOTE: disconnecting from ssh while running this command may leave zombie processes due to moby/moby#9098

```shell
dokku dolt:enter lollipop
```

You may also run a command directly against the service. Filesystem changes will not be saved to disk.

```shell
dokku dolt:enter lollipop touch /tmp/test
```

### expose a dolt service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)

```shell
# usage
dokku dolt:expose <service> <ports...>
```

Expose the service on the service's normal ports, allowing access to it from the public interface (`0.0.0.0`):

```shell
dokku dolt:expose lollipop 3306
```

Expose the service on the service's normal ports, with the first on a specified ip address (127.0.0.1):

```shell
dokku dolt:expose lollipop 127.0.0.1:3306
```

### unexpose a previously exposed dolt service

```shell
# usage
dokku dolt:unexpose <service>
```

Unexpose the service, removing access to it from the public interface (`0.0.0.0`):

```shell
dokku dolt:unexpose lollipop
```

### promote service <service> as DATABASE_URL in <app>

```shell
# usage
dokku dolt:promote <service> <app>
```

If you have a dolt service linked to an app and try to link another dolt service another link environment variable will be generated automatically:

```
DOKKU_DATABASE_BLUE_URL=mysql://other_service:ANOTHER_PASSWORD@dokku-dolt-other-service:3306/other_service
```

You can promote the new service to be the primary one:

> NOTE: this will restart your app

```shell
dokku dolt:promote other_service playground
```

This will replace `DATABASE_URL` with the url from other_service and generate another environment variable to hold the previous value if necessary. You could end up with the following for example:

```
DATABASE_URL=mysql://other_service:ANOTHER_PASSWORD@dokku-dolt-other-service:3306/other_service
DOKKU_DATABASE_BLUE_URL=mysql://other_service:ANOTHER_PASSWORD@dokku-dolt-other-service:3306/other_service
DOKKU_DATABASE_SILVER_URL=mysql://lollipop:SOME_PASSWORD@dokku-dolt-lollipop:3306/lollipop
```

### start a previously stopped dolt service

```shell
# usage
dokku dolt:start <service>
```

Start the service:

```shell
dokku dolt:start lollipop
```

### stop a running dolt service

```shell
# usage
dokku dolt:stop <service>
```

Stop the service and removes the running container:

```shell
dokku dolt:stop lollipop
```

### pause a running dolt service

```shell
# usage
dokku dolt:pause <service>
```

Pause the running container for the service:

```shell
dokku dolt:pause lollipop
```

### graceful shutdown and restart of the dolt service container

```shell
# usage
dokku dolt:restart <service>
```

Restart the service:

```shell
dokku dolt:restart lollipop
```

### upgrade service <service> to the specified versions

```shell
# usage
dokku dolt:upgrade <service> [--upgrade-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-R|--restart-apps "true"`: whether or not to force an app restart (default: false)
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for dolt docker container

You can upgrade an existing service to a new image or image-version:

```shell
dokku dolt:upgrade lollipop
```

### Service Automation

Service scripting can be executed using the following commands:

### list all dolt service links for a given app

```shell
# usage
dokku dolt:app-links <app>
```

List all dolt services that are linked to the `playground` app.

```shell
dokku dolt:app-links playground
```

### create container <new-name> then copy data from <name> into <new-name>

```shell
# usage
dokku dolt:clone <service> <new-service> [--clone-flags...]
```

flags:

- `-c|--config-options "--args --go=here"`: extra arguments to pass to the container create command (default: `None`)
- `-C|--custom-env "USER=alpha;HOST=beta"`: semi-colon delimited environment variables to start the service with
- `-i|--image IMAGE`: the image name to start the service with
- `-I|--image-version IMAGE_VERSION`: the image version to start the service with
- `-m|--memory MEMORY`: container memory limit in megabytes (default: unlimited)
- `-N|--initial-network INITIAL_NETWORK`: the initial network to attach the service to
- `-p|--password PASSWORD`: override the user-level service password
- `-P|--post-create-network NETWORKS`: a comma-separated list of networks to attach the service container to after service creation
- `-r|--root-password PASSWORD`: override the root-level service password
- `-S|--post-start-network NETWORKS`: a comma-separated list of networks to attach the service container to after service start
- `-s|--shm-size SHM_SIZE`: override shared memory size for dolt docker container

You can clone an existing service to a new one:

```shell
dokku dolt:clone lollipop lollipop-2
```

### check if the dolt service exists

```shell
# usage
dokku dolt:exists <service>
```

Here we check if the lollipop dolt service exists.

```shell
dokku dolt:exists lollipop
```

### check if the dolt service is linked to an app

```shell
# usage
dokku dolt:linked <service> <app>
```

Here we check if the lollipop dolt service is linked to the `playground` app.

```shell
dokku dolt:linked lollipop playground
```

### list all apps linked to the dolt service

```shell
# usage
dokku dolt:links <service>
```

List all apps linked to the `lollipop` dolt service.

```shell
dokku dolt:links lollipop
```

### Data Management

The underlying service data can be imported and exported with the following commands:

### import a dump into the dolt service database

```shell
# usage
dokku dolt:import <service>
```

Import a datastore dump:

```shell
dokku dolt:import lollipop < data.dump
```

### export a dump of the dolt service database

```shell
# usage
dokku dolt:export <service>
```

By default, datastore output is exported to stdout:

```shell
dokku dolt:export lollipop
```

You can redirect this output to a file:

```shell
dokku dolt:export lollipop > data.dump
```

### Backups

Datastore backups are supported via AWS S3 and S3 compatible services like [minio](https://github.com/minio/minio).

You may skip the `backup-auth` step if your dokku install is running within EC2 and has access to the bucket via an IAM profile. In that case, use the `--use-iam` option with the `backup` command.

Backups can be performed using the backup commands:

### set up authentication for backups on the dolt service

```shell
# usage
dokku dolt:backup-auth <service> <aws-access-key-id> <aws-secret-access-key> <aws-default-region> <aws-signature-version> <endpoint-url>
```

Setup s3 backup authentication:

```shell
dokku dolt:backup-auth lollipop AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY
```

Setup s3 backup authentication with different region:

```shell
dokku dolt:backup-auth lollipop AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_REGION
```

Setup s3 backup authentication with different signature version and endpoint:

```shell
dokku dolt:backup-auth lollipop AWS_ACCESS_KEY_ID AWS_SECRET_ACCESS_KEY AWS_REGION AWS_SIGNATURE_VERSION ENDPOINT_URL
```

More specific example for minio auth:

```shell
dokku dolt:backup-auth lollipop MINIO_ACCESS_KEY_ID MINIO_SECRET_ACCESS_KEY us-east-1 s3v4 https://YOURMINIOSERVICE
```

### remove backup authentication for the dolt service

```shell
# usage
dokku dolt:backup-deauth <service>
```

Remove s3 authentication:

```shell
dokku dolt:backup-deauth lollipop
```

### create a backup of the dolt service to an existing s3 bucket

```shell
# usage
dokku dolt:backup <service> <bucket-name> [--use-iam]
```

flags:

- `-u|--use-iam`: use the IAM profile associated with the current server

Backup the `lollipop` service to the `my-s3-bucket` bucket on `AWS`:`

```shell
dokku dolt:backup lollipop my-s3-bucket --use-iam
```

Restore a backup file (assuming it was extracted via `tar -xf backup.tgz`):

```shell
dokku dolt:import lollipop < backup-folder/export
```

### set encryption for all future backups of dolt service

```shell
# usage
dokku dolt:backup-set-encryption <service> <passphrase>
```

Set the GPG-compatible passphrase for encrypting backups for backups:

```shell
dokku dolt:backup-set-encryption lollipop
```

### set GPG Public Key encryption for all future backups of dolt service

```shell
# usage
dokku dolt:backup-set-public-key-encryption <service> <public-key-id>
```

Set the `GPG` Public Key for encrypting backups:

```shell
dokku dolt:backup-set-public-key-encryption lollipop
```

### unset encryption for future backups of the dolt service

```shell
# usage
dokku dolt:backup-unset-encryption <service>
```

Unset the `GPG` encryption passphrase for backups:

```shell
dokku dolt:backup-unset-encryption lollipop
```

### unset GPG Public Key encryption for future backups of the dolt service

```shell
# usage
dokku dolt:backup-unset-public-key-encryption <service>
```

Unset the `GPG` Public Key encryption for backups:

```shell
dokku dolt:backup-unset-public-key-encryption lollipop
```

### schedule a backup of the dolt service

```shell
# usage
dokku dolt:backup-schedule <service> <schedule> <bucket-name> [--use-iam]
```

flags:

- `-u|--use-iam`: use the IAM profile associated with the current server

Schedule a backup:

> 'schedule' is a crontab expression, eg. "0 3 * * *" for each day at 3am

```shell
dokku dolt:backup-schedule lollipop "0 3 * * *" my-s3-bucket
```

Schedule a backup and authenticate via iam:

```shell
dokku dolt:backup-schedule lollipop "0 3 * * *" my-s3-bucket --use-iam
```

### cat the contents of the configured backup cronfile for the service

```shell
# usage
dokku dolt:backup-schedule-cat <service>
```

Cat the contents of the configured backup cronfile for the service:

```shell
dokku dolt:backup-schedule-cat lollipop
```

### unschedule the backup of the dolt service

```shell
# usage
dokku dolt:backup-unschedule <service>
```

Remove the scheduled backup from cron:

```shell
dokku dolt:backup-unschedule lollipop
```

### Disabling `docker image pull` calls

If you wish to disable the `docker image pull` calls that the plugin triggers, you may set the `DOLT_DISABLE_PULL` environment variable to `true`. Once disabled, you will need to pull the service image you wish to deploy as shown in the `stderr` output.

Please ensure the proper images are in place when `docker image pull` is disabled.
