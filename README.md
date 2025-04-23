# Keycloak

## Install

```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm upgrade --install keycloak -n keycloak bitnami/keycloak --version 24.4.9 -f poc.yaml

kubectl get secret --namespace keycloak keycloak-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode ; echo
kubectl get secret --namespace keycloak keycloak-postgresql -o jsonpath="{.data.password}" | base64 --decode ; echo

## Delete
helm -n keycloak uninstall keycloak
kubectl -n keycloak delete pvc pvc-postgresql
```

## Resource

```yml
## Keycloak resource requests and limits
## ref: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
## @param resourcesPreset Set container resources according to one common preset (allowed values: none, nano, micro, small, medium, large, xlarge, 2xlarge). This is ignored if resources is set (resources is recommended for production).
## More information: https://github.com/bitnami/charts/blob/main/bitnami/common/templates/_resources.tpl#L15
##
resourcesPreset: "small"
## @param resources Set container requests and limits for different resources like CPU or memory (essential for production workloads)
## Example:
## resources:
##   requests:
##     cpu: 2
##     memory: 512Mi
##   limits:
##     cpu: 3
##     memory: 1024Mi
##
resources: {}
```

## PostgreSQL

```sh
## PostgreSQL chart configuration
## ref: https://github.com/bitnami/charts/blob/main/bitnami/postgresql/values.yaml
## @param postgresql.enabled Switch to enable or disable the PostgreSQL helm chart
## @param postgresql.auth.postgresPassword Password for the "postgres" admin user. Ignored if `auth.existingSecret` with key `postgres-password` is provided
## @param postgresql.auth.username Name for a custom user to create
## @param postgresql.auth.password Password for the custom user to create
## @param postgresql.auth.database Name for a custom database to create
## @param postgresql.auth.existingSecret Name of existing secret to use for PostgreSQL credentials
## @param postgresql.auth.secretKeys.userPasswordKey Name of key in existing secret to use for PostgreSQL credentials. Only used when `auth.existingSecret` is set.
## @param postgresql.architecture PostgreSQL architecture (`standalone` or `replication`)
##
postgresql:
  enabled: true
  auth:
    postgresPassword: "P@ssw0rd"
    username: bn_keycloak
    password: "P@ssw0rd"
    database: bitnami_keycloak
    existingSecret: ""
    secretKeys:
      userPasswordKey: password
  architecture: standalone
```

External PostgreSQL

```sh
## External PostgreSQL configuration
## All of these values are only used when postgresql.enabled is set to false
## @param externalDatabase.host Database host
## @param externalDatabase.port Database port number
## @param externalDatabase.user Non-root username for Keycloak
## @param externalDatabase.password Password for the non-root username for Keycloak
## @param externalDatabase.database Keycloak database name
## @param externalDatabase.existingSecret Name of an existing secret resource containing the database credentials
## @param externalDatabase.existingSecretHostKey Name of an existing secret key containing the database host name
## @param externalDatabase.existingSecretPortKey Name of an existing secret key containing the database port
## @param externalDatabase.existingSecretUserKey Name of an existing secret key containing the database user
## @param externalDatabase.existingSecretDatabaseKey Name of an existing secret key containing the database name
## @param externalDatabase.existingSecretPasswordKey Name of an existing secret key containing the database credentials
## @param externalDatabase.annotations Additional custom annotations for external database secret object
##
externalDatabase:
  host: ""
  port: 5432
  user: bn_keycloak
  database: bitnami_keycloak
  password: ""
  existingSecret: ""
  existingSecretHostKey: ""
  existingSecretPortKey: ""
  existingSecretUserKey: ""
  existingSecretDatabaseKey: ""
  existingSecretPasswordKey: ""
  annotations: {}
```

## Production mode

```sh
## @param production Run Keycloak in production mode. TLS configuration is required except when using proxy=edge.
##
production: false
```

## Authentication

```sh
## Keycloak authentication parameters
## ref: https://github.com/bitnami/containers/tree/main/bitnami/keycloak#admin-credentials
##
auth:
  ## @param auth.adminUser Keycloak administrator user
  ##
  adminUser: user
  ## @param auth.adminPassword Keycloak administrator password for the new user
  ##
  adminPassword: ""
  ## @param auth.existingSecret Existing secret containing Keycloak admin password
  ##
  existingSecret: ""
  ## @param auth.passwordSecretKey Key where the Keycloak admin password is being stored inside the existing secret.
  ##
  passwordSecretKey: ""
  ## @param auth.annotations Additional custom annotations for Keycloak auth secret object
  ##
  annotations: {}
```