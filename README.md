# vault-concourse-resource

Write a description of the resource here.

## Source Configuration

* `url`: *Required.* The URL of the vault you want to target.
* `role_id`: *Required.* The RoleID of the vault you are targeting.
* `secret_id`: *Required.* The SecretID of the vault you are targeting.
* `ca_cert`: *Optional.* The CA Certificate of the vault you are targeting.
* `namespace`: *Optional.* Vault Enterprise Namespace to target.
* `paths`: *Required.* The Secret paths you want to check.

## Behavior

### `check`: Check for something

Checks the paths and its secrets and creates a shasum
if secret(s) has changed the shasum will change

### `in`: Fetch something

Fetch all secrets recursivly assigned from provided paths
and puts them in a directory

### `out`: Put something somewhere

Import all secrets from a directory `path` to assigned vault

#### Parameters

* `path`: *Required.* The directory from the exported secrets from the IN step
* `prefix`: *Optional.* Prefix to use for the output path in vault.

## Example

```yaml
resource_types:
- name: vault-concourse-resource
  type: registry-image
  source:
    repository: starkandwayne/vault-concourse-resource

resources:
- name: vault-concourse-resource
  type: vault-concourse-resource
  check_every: 5m
  source:
    log_level: debug
    url: http://my.vault
    role_id: myroleid
    secret_id: mysecretid
    # new version if something under these paths changes
    paths:
    - /secret/handshake 

jobs:
- name: do-it
  plan:
  - get: vault-concourse-resource
    trigger: true
  - put: vault-concourse-resource
    params:
      path: vault-concourse-resource/secret
      prefix: secret2

```

## Development

### Prerequisites

* golang is *required* - version 1.15.x or higher is required.
* docker is *required* - version 17.05.x or higher is required.
* make is *required* - version 4.1 of GNU make is tested.

### Running the tests

The Makefile includes a `test` target, and tests are also run inside the Docker build.

Run the tests with the following command:

```sh
make test
```

### Building and publishing the image

The Makefile includes targets for building and publishing the docker image. Each of these
takes an optional `VERSION` argument, which will tag and/or push the docker image with
the given version.

```sh
make VERSION=1.2.3
make publish VERSION=1.2.3
```
