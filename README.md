# dokku dolt [![Build Status](https://img.shields.io/github/actions/workflow/status/bixoto/dokku-dolt/ci.yml?branch=master&style=flat-square "Build Status")](https://github.com/bixoto/dokku-dolt/actions/workflows/ci.yml?query=branch%3Amaster)

Unofficial dolt plugin for dokku, forked from the [mysql plugin](https://github.com/dokku/dokku-mysql). Currently defaults to installing [dolt 1.55.2](https://hub.docker.com/r/dolthub/dolt-sql-server).

## Requirements

- dokku 0.19.x+
- docker 1.8.x

## Installation

```shell
# on 0.19.x+
sudo dokku plugin:install https://github.com/bixoto/dokku-dolt.git --name dolt
```

-----------------------
Modified original README below:


## Commands

```
dolt:app-links <app>                              # list all dolt service links for a given app
dolt:backup <service> <bucket-name> [--use-iam]   # create a backup of the dolt service to an existing s3 bucket
dolt:backup-auth <service> <aws-access-key-id> <aws-secret-access-key> <aws-default-region> <aws-signature-version> <endpoint-url> # set up authentication for backups on the dolt service
dolt:backup-deauth <service>                      # remove backup authentication for the dolt service
dolt:backup-schedule <service> <schedule> <bucket-name> [--use-iam] # schedule a backup of the dolt service
dolt:backup-schedule-cat <service>                # cat the contents of the configured backup cronfile for the service
dolt:backup-set-encryption <service> <passphrase> # set encryption for all future backups of dolt service
dolt:backup-set-public-key-encryption <service> <public-key-id> # set GPG Public Key encryption for all future backups of dolt service
dolt:backup-unschedule <service>                  # unschedule the backup of the dolt service
dolt:backup-unset-encryption <service>            # unset encryption for future backups of the dolt service
dolt:backup-unset-public-key-encryption <service> # unset GPG Public Key encryption for future backups of the dolt service
dolt:clone <service> <new-service> [--clone-flags...] # create container <new-name> then copy data from <name> into <new-name>
dolt:connect <service>                            # connect to the service via the dolt connection tool
dolt:create <service> [--create-flags...]         # create a dolt service
dolt:destroy <service> [-f|--force]               # delete the dolt service/data/container if there are no links left
dolt:enter <service>                              # enter or run a command in a running dolt service container
dolt:exists <service>                             # check if the dolt service exists
dolt:export <service>                             # export a dump of the dolt service database
dolt:expose <service> <ports...>                  # expose a dolt service on custom host:port if provided (random port on the 0.0.0.0 interface if otherwise unspecified)
dolt:import <service>                             # import a dump into the dolt service database
dolt:info <service> [--single-info-flag]          # print the service information
dolt:link <service> <app> [--link-flags...]       # link the dolt service to the app
dolt:linked <service> <app>                       # check if the dolt service is linked to an app
dolt:links <service>                              # list all apps linked to the dolt service
dolt:list                                         # list all dolt services
dolt:logs <service> [-t|--tail] <tail-num-optional> # print the most recent log(s) for this service
dolt:pause <service>                              # pause a running dolt service
dolt:promote <service> <app>                      # promote service <service> as DATABASE_URL in <app>
dolt:restart <service>                            # graceful shutdown and restart of the dolt service container
dolt:set <service> <key> <value>                  # set or clear a property for a service
dolt:start <service>                              # start a previously stopped dolt service
dolt:stop <service>                               # stop a running dolt service
dolt:unexpose <service>                           # unexpose a previously exposed dolt service
dolt:unlink <service> <app>                       # unlink the dolt service from the app
dolt:upgrade <service> [--upgrade-flags...]       # upgrade service <service> to the specified versions
```

## Usage

Help for any commands can be displayed by specifying the command as an argument to `dolt:help`.
Plugin help output in conjunction with any files in the `docs/` folder is used to generate the plugin documentation.
Please consult the `dolt:help` command for any undocumented commands.

### Basic Usage

...
