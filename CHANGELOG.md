# Change Log

All notable changes to this project will be documented in this file.
This project adheres to [Semantic Versioning](http://semver.org/).

## [Unreleased](https://github.com/passbolt/charts-passbolt/compare/v0.2.0...HEAD)

## [0.2.0] - 2023-03-17

This release contains breaking changes!!!

In order to support rootless container images we have removed the installation of php-redis
during the deployment and moved such dependency to passbolt debian packages. (Rootless container images
do not allow to install packages for obvious reasons).
By moving the php-redis dependency to our debian packages there is no need to install anything
during the deployment of this chart.

The downside however is that now this chart requires passbolt-3.12.0-3 as minimal docker image.

We have also include a few contributions from the community, thanks to all of you who helped during this release!

### Added
- [#6](https://github.com/passbolt/charts-passbolt/pull/6) make kubectl more flexible
- Support for rootless images in HA scenarios

### Fixed
- [#9](https://github.com/passbolt/charts-passbolt/pull/9) set default value for EMAIL_TRANSPORT_DEFAULT_HOST

## [0.1.4] - 2023-03-06

- Bump passbolt docker image tag to 3.11.1-1

## [0.1.3] - 2023-03-02

- Bump passbolt docker image tag to 3.11.0-1

## [0.1.2] - 2023-02-21

### Added

- Merged [#3](https://github.com/passbolt/charts-passbolt/pull/3)
- Added test for gpg volumes on cronjob

## [0.1.1] - 2023-02-10

### Added

- Bump passbolt default version to 3.10.0-1-ce
- Bump passbolt-library default version to 0.2.7
- Readme images

## [0.1.0] - 2023-02-02

### Added

- Automatic generation of server keys if not provided.
- Support for multiple Passbolt pods by using a redis proxy and storing the sessions in redis cache.
- Kubernetes cronjob to process emails.
- Unit test for multiple and not all resources.
- Support for rbac, ingress and network policies.
