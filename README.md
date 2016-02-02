# mantl-universe

The Mantl Universe package repository - install DCOS packages on [Mantl](https://github.com/CiscoCloud/microservices-infrastructure) with [Mantl API](https://github.com/CiscoCloud/mantl-api/).

**As of mantl-api 0.1.4, the default mantl-universe branch is `version-0.6`.**

## Packages

The goals of the packaging system are:

1. Make it easy to contribute packages to Mantl
2. Rely on existing DCOS packages when possible
3. Immutable package versions
4. Ability for users to build customized package versions

### Package Structure

Mantl Universe packages are based on the [DCOS packaging format](https://github.com/mesosphere/universe/#organization) from Mesosphere. Packages in Mantl Universe are intended to be installed with [Mantl API](https://github.com/CiscoCloud/mantl-api/) and may depend on the corresponding package version in the [Mesosphere Universe](https://github.com/mesosphere/universe/). For details on how Mantl API installs packages, you can review the [Mantl API README](https://github.com/CiscoCloud/mantl-api/blob/master/README.md).

Each package is made up of several json files:

#### package.json

The [package.json](https://github.com/mesosphere/universe/#packagejson) file is where package metadata is set. This includes things like the name, version, description, and maintainer of the package.

In Mantl Universe, the `package.json` is optional if the intention is to use the `package.json` from the corresponding package version in the [Mesosphere Universe](https://github.com/mesosphere/universe/).

#### config.json

The [config.json](https://github.com/mesosphere/universe/#configjson) file contains the configuration schema and defaults for the package.

In Mantl Universe, the `config.json` is optional if the intention is to use the `config.json` from the corresponding package version in the [Mesosphere Universe](https://github.com/mesosphere/universe/).

#### mantl.json

The `mantl.json` overrides configuration defaults from the `config.json` file (either directly in the package or from the `config.json` for the corresponding package version in the Mesosphere universe) with values that are known to work on Mantl.

Also, the presence of the mantl.json dictates whether the package is "supported" on Mantl. It can be empty `{}` if there is no specific changes required to make the package run on Mantl.

`mantl.json` includes a setting that allows you to control how the application is load balanced on Mantl clusters. Currently, it is a simple toggle that allows you to control whether the application will be load balanced by [Traefik](https://traefik.github.io) in a Mantl cluster. Here is an example `mantl.json` with no other settings other than the load balancing configuration:

```json
{
  "mantl": {
    "load-balancer": "external|off"
  }
}
```

If `mantl.load-balancer` is set to "external", the application will be included in the Traefik load balancer. Any other value will disable load balancing.

#### marathon.json

The [marathon.json](https://github.com/mesosphere/universe/#marathonjson) file contains the [Marathon application json](https://mesosphere.github.io/marathon/docs/rest-api.html#post-v2-apps) for the package. It is a [mustache](https://mustache.github.io/) template that is rendered using data from `config.json` and `mantl.json`.

In Mantl Universe, the `marathon.json` is optional if the intention is to use the `marathon.json` from the corresponding package version in the [Mesosphere Universe](https://github.com/mesosphere/universe/).

#### uninstall.json

The `uninstall.json` defines additional behavior when uninstalling a package. The uninstall support is currently limited to removing zookeeper nodes. It is also a mustache template so that variables from `config.json` and `mantl.json` can be used in the uninstall definition.

```json
{
  "zookeeper": {
    "delete": [
      {
        "path": "{{kafka.storage}}",
        "always": true
      }
    ]
  }
}
```

*Example uninstall.json that deletes zookeeper nodes with a path based on the `kafka.storage` variable.*

### Available Packages

Mantl Universe provides the following Mantl supported packages:

* Arangodb
* Cassandra
* Chronos
* Elasticsearch
* HDFS
* Kafka
