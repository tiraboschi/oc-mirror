# oc-mirror

**This repo is under active development. CLI and APIs are unstable**

- [oc-mirror](#oc-mirror)
  - [Usage](#usage)
    - [Environment Prep](#environment-prep)
    - [Content Discovery](#content-discovery)
      - [Updates](#updates)
      - [Releases](#releases)
      - [Operators](#operators)
    - [Test](#test)

`oc-mirror` is an OpenShift Client (oc) plugin that manages OpenShift release, operator catalog, helm charts, and associated container images.
## Usage

The mirror registry `reg.mirror.com` is used in this example.
Replace this value with a real registry host, or create a `docker.io/library/registry:2` container locally.

### Environment Prep
1. Download pull secret and place at `~/.docker/config.json`<sup>1</sup>.
    - Your mirror registry secret must have both push and pull scopes.
2. Build:
    ```sh
    make build
    ```
### Content Discovery

#### Updates

- List updates since the last `oc-mirror` run
  ```sh
  ./bin/oc-mirror list updates --config imageset-config.yaml --dir test-create
  ```
#### Releases
1. List all available release payloads for a version of OpenShift (defaults to stable)
   ```sh
   ./bin/oc-mirror list releases --version=4.9
   ```
2. List all available channels to query for a version of OpenShift
   ```sh
   ./bin/oc-mirror list releases --channels --version=4.8
   ```
3. List all available release payloads for a version of OpenShift in a specified channel
   ```sh
   ./bin/oc-mirror list releases --channel=fast-4.9
   ```
#### Operators
1. List all available catalogs for a version of OpenShift
   ```sh
   ./bin/oc-mirror list operators --catalogs --version=4.9
   ```
2. List all available packages in a catalog
   ```sh
   ./bin/oc-mirror list operators --catalog=catalog-name
   ````
3. List all available channels in a package
    ```sh
    ./bin/oc-mirror list operators --catalog=catalog-name --package=package-name
    ```
4. List all available versions in a channel
      ```sh
    ./bin/oc-mirror list operators --catalog=catalog-name --package=package-name --channel=channel-name
    ```
### Mirroring

#### Fully Disconnected
- Create then publish to your mirror registry:
    ```sh
    ./bin/oc-mirror --config imageset-config.yaml --dir test-create file://archives
    ./bin/oc-mirror --from /path/to/archives --dir test-publish docker://reg.mirror.com
    ```
#### Partially Disconnected
- Publish mirror to mirror
     ```sh
    ./bin/oc-mirror --config imageset-config.yaml --dir test docker://localhost:5000
    ```
## Additional Features
- Get information on your imageset using `describe`
    ```sh
    ./bin/oc-mirror describe /path/to/archives
    ```
For configuration and options, see the [expanded overview](./docs/overview.md) and [usage](./docs/usage.md) docs.

<sup>1</sup> For this example, the `create` and `publish` steps are run on the same machine. Therefore your `~/.docker/config.json`
should contain auth config for both release/catalog source images _and_ your mirror registry.

## oc-mirror Spec

See the [config spec][config-spec] for an in-depth description of fields.

**Note:** The `imageset-config.yaml` is only used during bundle creation.

## Development

### Requirements

- All top-level requirements
- [`go`][go] version 1.16+

### Build

```sh
make
./bin/oc-mirror -h
```

### Test

Unit:
```sh
make test-unit
```

E2E:
```sh
make test-e2e
```

[config-spec]:https://pkg.go.dev/github.com/openshift/oc-mirror/pkg/config/v1alpha1#ImageSetConfiguration
[go]:https://golang.org/dl/