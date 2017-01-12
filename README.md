# moses
[![Runtime][runtime-badge]][moses-runtime-url]
[![License][license-badge]][moses-license-url]

`moses` is the missing tool to handle snapshots of [ElasticSearch][es-url] that are hosted in [AWS S3][aws-s3-url].

With `moses` you will be able in your ElasticSearch to:

- **Create** snapshots;
- **Delete** snapshots;
- **Restore** snapshots;
- **List** snapshots;

It was also design to handle multiple environments through configuration files.

Another great feature is that you can define when your snapshot will expire,
making it easy to implement a life cycle on them.

## Requirements

### AWS

* A `bucket` in AWS S3;
* An user in AWS IAM with _Access Key_ and _Secret Key_;
* A policy in AWS IAM with the permissions described in [repository-s3 official documentation][es-plugin-s3-doc-url];

### ElasticSearch

* ElasticSearch 5
* `repository-s3` plugin installed

**Important**: Only tested in ElasticSearch 5, but probably will work with lowers versions of ElasticSearch.

### Linux

* curl
* [jq][jq-url]

## Configuration

A sample environment configuration.

```properties
# aws region
awsRegion="us-west"
# aws access key
awsAccessKey="AWS_ACCESS_KEY_ID"
# aws secret key
awsSecretKey="AWS_SECRET_ACCESS_KEY"
# aws s3 repository responsible for snapshots
esRepository="s3-repository"
# elasticsearch master address
esMasterAddress="127.0.0.1"
# how many days we need to keep snapshots
retainDays="10"
# how many snapshots we need to keep
# value of -1 disable this feature
snapsKeep="10"
```

## Usage

How **list** snapshots with `moses`.

```shell
$ moses -e sample.conf -m list
Loading environment "sample.conf" configurations...
Checking if "127.0.0.1" is responding...
Good! "127.0.0.1" is up and running.
Checking if "s3-repository" exists inside ElasticSearch...
Good! "s3-repository" already exists.
Listing all snapshots...
- "snapshot_20170111-180906" [expired]
- "snapshot_20170111-180927" [expired]
- "snapshot_20170111-181021" [expired]
- "snapshot_20170111-181029"
- "snapshot_20170111-181036"
- "snapshot_20170111-181043"
- "snapshot_20170112-094451"
```

How **create** snapshots with `moses`.

```shell
$ moses -e sample.conf -m create
Loading environment "sample.conf" configurations...
Checking if "127.0.0.1" is responding...
Good! "127.0.0.1" is up and running.
Checking if "s3-repository" exists inside ElasticSearch...
Good! "s3-repository" already exists.
Detecting if there's any outdated snapshots...
Listing all snapshots...
- "snapshot_20170111-180906" [expired]
- "snapshot_20170111-180927" [expired]
- "snapshot_20170111-181021" [expired]
- "snapshot_20170111-181029"
- "snapshot_20170111-181036"
- "snapshot_20170111-181043"
- "snapshot_20170112-094451"
Skipping delete as you have 10 or less snapshots...
Creating "snapshot_20170112-180059"...
Snapshot "snapshot_20170112-180059" was successfully created!
Listing all snapshots...
- "snapshot_20170111-180906" [expired]
- "snapshot_20170111-180927" [expired]
- "snapshot_20170111-181021" [expired]
- "snapshot_20170111-181029"
- "snapshot_20170111-181036"
- "snapshot_20170111-181043"
- "snapshot_20170112-094451"
- "snapshot_20170112-180059"
```

How **delete** snapshots with `moses`.

```shell
$ moses -e sample.conf -m delete
Loading environment "sample.conf" configurations...
Checking if "127.0.0.1" is responding...
Good! "127.0.0.1" is up and running.
Checking if "s3-repository" exists inside ElasticSearch...
Good! "s3-repository" already exists.
Listing all snapshots...
- "snapshot_20170111-180906" [expired]
- "snapshot_20170111-180927" [expired]
- "snapshot_20170111-181021" [expired]
- "snapshot_20170111-181029"
- "snapshot_20170111-181036"
- "snapshot_20170111-181043"
- "snapshot_20170112-094451"
- "snapshot_20170112-180059"
Select a snapshot to delete: snapshot_20170112-180059
Warning: This is going to delete "snapshot_20170112-180059" snapshot.
Are you sure? (yes/no) yes
Deleting "snapshot_20170112-180059"...
Snapshot "snapshot_20170112-180059" was successfully deleted!
```

How **restore** snapshots with `moses`.

```shell
$ moses -e sample.conf -m restore
Loading environment "sample.conf" configurations...
Checking if "127.0.0.1" is responding...
Good! "127.0.0.1" is up and running.
Checking if "s3-repository" exists inside ElasticSearch...
Good! "s3-repository" already exists.
Listing all snapshots...
- "snapshot_20170111-180906" [expired]
- "snapshot_20170111-180927" [expired]
- "snapshot_20170111-181021" [expired]
- "snapshot_20170111-181029"
- "snapshot_20170111-181036"
- "snapshot_20170111-181043"
- "snapshot_20170112-094451"
Select a snapshot to restore: snapshot_20170111-180906
Warning: This is going to restore "snapshot_20170111-180906" snapshot.
Are you sure? (yes/no) yes
Restoring "snapshot_20170111-180906"...
Snapshot "snapshot_20170111-180906" was successfully restored!
```

## Changes

See `CHANGELOG.md` for more details.

## License and Author

See `LICENSE` for more details.

## Trademark

Elasticsearch is a trademark of Elasticsearch BV,
registered in the U.S. and in other countries.

Amazon Web Services and AWS are trademarks of Amazon.com, Inc.
or its affiliates in the United States and/or other countries.

   [aws-s3-url]: https://aws.amazon.com/s3
   [es-url]: https://www.elastic.co
   [jq-url]: https://stedolan.github.io/jq/
   [es-plugin-s3-doc-url]: https://www.elastic.co/guide/en/elasticsearch/plugins/current/repository-s3-repository.html
   [moses-runtime-url]: https://github.com/amalucelli/moses
   [moses-license-url]: https://github.com/amalucelli/moses/blob/master/LICENSE
   [license-badge]: https://img.shields.io/badge/license-apache-757575.svg?style=flat-square
   [runtime-badge]: https://img.shields.io/badge/runtime-shell-orange.svg?style=flat-square
