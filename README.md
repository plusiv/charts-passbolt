# Passbolt Helm chart

<h3 align="center">
    <img src="./.assets/helm_passbolt.png" alt="passbolt sails kubernetes" width="500"/>
</h3>

![Version: 0.2.0](https://img.shields.io/badge/Version-0.2.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: 3.12.0-3-ce](https://img.shields.io/badge/AppVersion-3.12.0--3--ce-informational?style=flat-square)

Passbolt is an open source, security first password manager with strong focus on
collaboration.

## TL;DR

The following command is not recommended for production deployments as it will
use default passwords for internal databases:

```bash
helm repo add my-repo https://download.passbolt.com/charts/passbolt
helm install my-release my-repo/passbolt
```

Production workloads should change the fields with values 'CHANGEME' on values.yaml
and deploy the chart as follows:

```bash
helm repo add my-repo https://download.passbolt.com/charts/passbolt
helm install my-release my-repo/passbolt -f values.yaml
```

Or using `--set` flags to modify certain chart options:

```bash
helm repo add my-repo https://download.passbolt.com/charts/passbolt
helm install my-release my-repo/passbolt \
  --set redis.auth.password=my_redis_password \
  --set passboltEnv.CACHE_CAKE_DEFAULT_PASSWORD=my_redis_password \
  --set mariadb.auth.password=my_mariadb_password \
  --set passboltEnv.secret.DATASOURCES_DEFAULT_PASSWORD=my_mariadb_password
```

## Introduction

This chart deploys [passbolt](https://www.passbolt.com) on [kubernetes](https://kubernetes.io) using the [Helm](https://helm.sh/) package manager.

Passbolt comes in three editions:

- [Community edition](https://www.passbolt.com/ce/docker)
- [Professional edition](https://signup.passbolt.com/pricing/pro)
- [Cloud edition](https://signup.passbolt.com/pricing/cloud)

This chart supports the deployment of Community edition and Professional edition.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.x
- Passbolt docker >= 3.12.0-3

## Installing the chart

Installing the chart under the name `my-release`:

```bash
helm install my-release my-repo
```

The above command deploys passbolt with default settings on your kubernetes cluster.
Check the [configuration](#Configuration) section to check which parameters you can fine tune.

## Creating first user

Once the chart is deployed, you can create your first user by running the following command:

```bash
kubectl exec -it <passbolt-pod-name> -- su -c "bin/cake passbolt register_user -u <email> -f <firstname> -l <lastname> -r admin" -s /bin/bash www-data
```


## Uninstalling the chart

To uninstall/delete the chart from your cluster:

```bash
helm delete my-release
````

The above command deletes all the kubernetes components associated with the
chart and deletes the release.

## Requirements

| Repository | Name | Version |
|------------|------|---------|
| https://charts.bitnami.com/bitnami | mariadb | 11.3.5 |
| https://charts.bitnami.com/bitnami | redis | 17.3.8 |
| https://download.passbolt.com/charts/passbolt-library | passbolt-library | 0.2.7 |

## Values

| Key                                                           | Type   | Default                                                                                                               | Description                                                                                                                                                               |
|---------------------------------------------------------------|--------|-----------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| affinity                                                      | object | `{}`                                                                                                                  | Configure passbolt deployment affinity                                                                                                                                    |
| app.cache.redis.enabled                                       | bool   | `true`                                                                                                                | By enabling redis the chart will mount a configuration file on /etc/passbolt/app.php That instructs passbolt to store sessions on redis and to use it as a general cache. |
| app.cache.redis.sentinelProxy.enabled                         | bool   | `true`                                                                                                                | Inject a haproxy sidecar container configured as a proxy to redis sentinel Make sure that CACHE_CAKE_DEFAULT_SERVER is set to '127.0.0.1' to use the proxy                |
| app.cache.redis.sentinelProxy.image                           | object | `{"repository":"haproxy","tag":"latest"}`                                                                             | Configure redis sentinel proxy image                                                                                                                                      |
| app.cache.redis.sentinelProxy.image.repository                | string | `"haproxy"`                                                                                                           | Configure redis sentinel image repository                                                                                                                                 |
| app.cache.redis.sentinelProxy.image.tag                       | string | `"latest"`                                                                                                            | Configure redis sentinel image tag                                                                                                                                        |
| app.image.pullPolicy                                          | string | `"IfNotPresent"`                                                                                                      | Configure pasbolt deployment image pullPolicy                                                                                                                             |
| app.image.repository                                          | string | `"passbolt/passbolt"`                                                                                                 | Configure pasbolt deployment image repsitory                                                                                                                              |
| app.image.tag                                                 | string | `"3.12.0-3-ce"`                                                                                                      | Overrides the image tag whose default is the chart appVersion.                                                                                                            |
| app.initImage.pullPolicy                                      | string | `"IfNotPresent"`                                                                                                      | Configure pasbolt deployment image pullPolicy                                                                                                                             |
| app.initImage.repository                                      | string | `"mariadb"`                                                                                                           | Configure pasbolt deployment image repsitory                                                                                                                              |
| app.initImage.tag                                             | string | `"latest"`                                                                                                            | Overrides the image tag whose default is the chart appVersion.                                                                                                            |
| app.resources                                                 | object | `{}`                                                                                                                  |                                                                                                                                                                           |
| autoscaling.enabled                                           | bool   | `false`                                                                                                               | Enable autoscaling on passbolt deployment                                                                                                                                 |
| autoscaling.maxReplicas                                       | int    | `100`                                                                                                                 | Configure autoscaling maximum replicas                                                                                                                                    |
| autoscaling.minReplicas                                       | int    | `1`                                                                                                                   | Configure autoscaling minimum replicas                                                                                                                                    |
| autoscaling.targetCPUUtilizationPercentage                    | int    | `80`                                                                                                                  | Configure autoscaling target CPU uptilization percentage                                                                                                                  |
| cronJobEmail                                                  | object | `{"enabled":true,"schedule":"* * * * *"}`                                                                             | Enable email cron                                                                                                                                                         |
| fullnameOverride                                              | string | `""`                                                                                                                  | Value to override the whole fullName                                                                                                                                      |
| gpgPath                                                       | string | `"/etc/passbolt/gpg"`                                                                                                 | Configure passbolt gpg directory                                                                                                                                          |
| gpgServerKeyPrivate                                           | string | `""`                                                                                                                  | Gpg server private key in base64                                                                                                                                          |
| gpgServerKeyPublic                                            | string | `""`                                                                                                                  | Gpg server public key in base64                                                                                                                                           |
| imagePullSecrets                                              | list   | `[]`                                                                                                                  | Configure image pull secrets                                                                                                                                              |
| ingress.annotations                                           | object | `{}`                                                                                                                  | Configure passbolt ingress annotations                                                                                                                                    |
| ingress.enabled                                               | bool   | `false`                                                                                                               | Enable passbolt ingress                                                                                                                                                   |
| ingress.hosts                                                 | list   | `[]`                                                                                                                  | Configure passbolt ingress hosts                                                                                                                                          |
| ingress.tls                                                   | list   | `[]`                                                                                                                  | Configure passbolt ingress tls                                                                                                                                            |
| jwtPath                                                       | string | `"/etc/passbolt/jwt"`                                                                                                 | Configure passbolt jwt directory                                                                                                                                          |
| jwtServerPrivate                                              | string | `nil`                                                                                                                 | JWT server private key in base64                                                                                                                                          |
| jwtServerPublic                                               | string | `nil`                                                                                                                 | JWT server public key in base64                                                                                                                                           |
| livenessProbe                                                 | object | `{"initialDelaySeconds":20,"periodSeconds":10}`                                                                       | Configure passbolt container livenessProbe                                                                                                                                |
| mariadb.architecture                                          | string | `"replication"`                                                                                                       | Configure mariadb architecture                                                                                                                                            |
| mariadb.auth.database                                         | string | `"passbolt"`                                                                                                          | Configure mariadb auth database                                                                                                                                           |
| mariadb.auth.password                                         | string | `"CHANGEME"`                                                                                                          | Configure mariadb auth password                                                                                                                                           |
| mariadb.auth.replicationPassword                              | string | `"CHANGEME"`                                                                                                          | Configure mariadb auth replicationPassword                                                                                                                                |
| mariadb.auth.rootPassword                                     | string | `"root"`                                                                                                              | Configure mariadb auth root password                                                                                                                                      |
| mariadb.auth.username                                         | string | `"CHANGEME"`                                                                                                          | Configure mariadb auth username                                                                                                                                           |
| mariadbDependencyEnabled                                      | bool   | `true`                                                                                                                | Install mariadb as a depending chart                                                                                                                                      |
| nameOverride                                                  | string | `""`                                                                                                                  | Value to override the chart name on default                                                                                                                               |
| networkPolicy.enabled                                         | bool   | `false`                                                                                                               | Enable network policies to allow ingress access passbolt pods                                                                                                             |
| networkPolicy.label                                           | string | `"app.kubernetes.io/name"`                                                                                            | Configure network policies label for ingress deployment                                                                                                                   |
| networkPolicy.namespaceLabel                                  | string | `"ingress-nginx"`                                                                                                     | Configure network policies namespaceLabel for namespaceSelector                                                                                                           |
| networkPolicy.podLabel                                        | string | `"ingress-nginx"`                                                                                                     | Configure network policies podLabel for podSelector                                                                                                                       |
| nodeSelector                                                  | object | `{}`                                                                                                                  | Configure passbolt deployment nodeSelector                                                                                                                                |
| passboltEnv.plain.APP_FULL_BASE_URL                           | string | `"https://passbolt.local"`                                                                                            | Configure passbolt fullBaseUrl                                                                                                                                            |
| passboltEnv.plain.CACHE_CAKE_DEFAULT_SERVER                   | string | `"127.0.0.1"`                                                                                                         | Configure passbolt cake cache server                                                                                                                                      |
| passboltEnv.plain.DEBUG                                       | bool   | `false`                                                                                                               | Toggle passbolt debug mode                                                                                                                                                |
| passboltEnv.plain.EMAIL_DEFAULT_FROM                          | string | `"no-reply@passbolt.local"`                                                                                           | Configure passbolt default email from                                                                                                                                     |
| passboltEnv.plain.EMAIL_TRANSPORT_DEFAULT_HOST                | string | `"127.0.0.1"`                                                                                                         | Configure passbolt default email host                                                                                                                                     |
| passboltEnv.plain.EMAIL_TRANSPORT_DEFAULT_PORT                | int    | `587`                                                                                                                 | Configure passbolt default email service port                                                                                                                             |
| passboltEnv.plain.EMAIL_TRANSPORT_DEFAULT_TLS                 | bool   | `true`                                                                                                                | Toggle passbolt tls                                                                                                                                                       |
| passboltEnv.plain.KUBECTL_DOWNLOAD_CMD                        | string | `"curl -LO \"https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl\""` | Download Command for kubectl                                                                                                                                              |
| passboltEnv.plain.PASSBOLT_JWT_SERVER_KEY                     | string | `"/var/www/passbolt/config/jwt/jwt.key"`                                                                              | Configure passbolt jwt private key path                                                                                                                                   |
| passboltEnv.plain.PASSBOLT_JWT_SERVER_PEM                     | string | `"/var/www/passbolt/config/jwt/jwt.pem"`                                                                              | Configure passbolt jwt public key path                                                                                                                                    |
| passboltEnv.plain.PASSBOLT_KEY_EMAIL                          | string | `"passbolt@yourdomain.com"`                                                                                           | Configure email used on gpg key. This is used when automatically creating a new gpg server key and when automatically calculating the fingerprint.                        |
| passboltEnv.plain.PASSBOLT_LEGAL_PRIVACYPOLICYURL             | string | `"https://www.passbolt.com/privacy"`                                                                                  | Configure passbolt privacy url                                                                                                                                            |
| passboltEnv.plain.PASSBOLT_PLUGINS_JWT_AUTHENTICATION_ENABLED | bool   | `true`                                                                                                                | Toggle passbolt jwt authentication                                                                                                                                        |
| passboltEnv.plain.PASSBOLT_PLUGINS_LICENSE_LICENSE            | string | `"/etc/passbolt/subscription_key.txt"`                                                                                | Configure passbolt license path                                                                                                                                           |
| passboltEnv.plain.PASSBOLT_REGISTRATION_PUBLIC                | bool   | `true`                                                                                                                | Toggle passbolt public registration                                                                                                                                       |
| passboltEnv.plain.PASSBOLT_SELENIUM_ACTIVE                    | bool   | `false`                                                                                                               | Toggle passbolt selenium mode                                                                                                                                             |
| passboltEnv.plain.PASSBOLT_SSL_FORCE                          | bool   | `true`                                                                                                                | Configure passbolt to force ssl                                                                                                                                           |
| passboltEnv.secret.CACHE_CAKE_DEFAULT_PASSWORD                | string | `"CHANGEME"`                                                                                                          | Configure passbolt cake cache password                                                                                                                                    |
| passboltEnv.secret.DATASOURCES_DEFAULT_DATABASE               | string | `"passbolt"`                                                                                                          | Configure passbolt default database                                                                                                                                       |
| passboltEnv.secret.DATASOURCES_DEFAULT_PASSWORD               | string | `"CHANGEME"`                                                                                                          | Configure passbolt default database password                                                                                                                              |
| passboltEnv.secret.DATASOURCES_DEFAULT_USERNAME               | string | `"CHANGEME"`                                                                                                          | Configure passbolt default database username                                                                                                                              |
| passboltEnv.secret.EMAIL_TRANSPORT_DEFAULT_PASSWORD           | string | `"CHANGEME"`                                                                                                          | Configure passbolt default email service password                                                                                                                         |
| passboltEnv.secret.EMAIL_TRANSPORT_DEFAULT_USERNAME           | string | `"CHANGEME"`                                                                                                          | Configure passbolt default email service username                                                                                                                         |
| podAnnotations                                                | object | `{}`                                                                                                                  | Map of annotation for passbolt server pod                                                                                                                                 |
| podSecurityContext                                            | object | `{}`                                                                                                                  | Security Context configuration for passbolt server pod                                                                                                                    |
| rbacEnabled                                                   | bool   | `true`                                                                                                                | Enable role based access control                                                                                                                                          |
| readinessProbe                                                | object | `{"initialDelaySeconds":5,"periodSeconds":10}`                                                                        | Configure passbolt container RadinessProbe                                                                                                                                |
| redis.auth.enabled                                            | bool   | `true`                                                                                                                | Enable redis authentication                                                                                                                                               |
| redis.auth.password                                           | string | `"CHANGEME"`                                                                                                          | Configure redis password                                                                                                                                                  |
| redis.sentinel.enabled                                        | bool   | `true`                                                                                                                | Enable redis sentinel                                                                                                                                                     |
| redisDependencyEnabled                                        | bool   | `true`                                                                                                                | Install redis as a depending chart                                                                                                                                        |
| replicaCount                                                  | int    | `2`                                                                                                                   | If autoscaling is disabled this will define the number of pods to run                                                                                                     |
| service.name                                                  | string | `"https"`                                                                                                             | Configure passbolt service port name                                                                                                                                      |
| service.port                                                  | int    | `443`                                                                                                                 | Configure passbolt service port                                                                                                                                           |
| service.targetPort                                            | int    | `443`                                                                                                                 | Configure passbolt service targetPort                                                                                                                                     |
| service.type                                                  | string | `"ClusterIP"`                                                                                                         | Configure passbolt service type                                                                                                                                           |
| serviceAccount.annotations                                    | object | `{}`                                                                                                                  | Annotations to add to the service account                                                                                                                                 |
| serviceAccount.create                                         | bool   | `true`                                                                                                                | Specifies whether a service account should be created                                                                                                                     |
| tolerations                                                   | list   | `[]`                                                                                                                  | Configure passbolt deployment tolerations                                                                                                                                 |

