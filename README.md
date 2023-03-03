# microk8s-content-demo-snap

Snap showing how to use the configuration launcher in MicroK8s.

## Develop and Build

### Configuration

Create a YAML file called `launcher/configuration/content.yaml` with the launch configuration you want to apply to the MicroK8s instance. In this example snap, we only enable a few addons.

The configuration schema can be found in [Configuration](https://github.com/canonical/microk8s-cluster-agent/blob/main/pkg/k8sinit/schema.go#L43). Also see [full.yaml](https://github.com/canonical/microk8s-cluster-agent/blob/main/pkg/k8sinit/testdata/schema/full.yaml) for an example.

```yaml
# launcher/configuration/content.yaml
---
version: 0.1.0
addons:
  - name: dns
  - name: rbac
  - name: ingress
```

### Image Sideloading

Create one or more `tar` archives containing OCI images that will be sideloaded into the cluster and add them under `launcher/sideload/<image>.tar`. The filename does not matter, but the file extension must be `.tar`.

By default, this snap only contains the pause image (`registry.k8s.io/pause:3.7`) due to size considerations.

```bash
# retrieve pause.tar from a running instance:
microk8s ctr images export-local pause.tar registry.k8s.io/pause:3.7

# copy to launcher/sideload/
mkdir -p launcher/sideload
cp pause.tar launcher/sideload/pause.tar
```

### Build snap

Build the snap using snapcraft. This will produce a `content-demo-microk8s_<version>_<arch>.snap` file.

```
snapcraft
```

Optionally, upload and release the snap to the Snap Store:

```
snapcraft upload ./content-demo-microk8s_*.snap --release latest/edge
```

## Usage
Install microk8s from a strict channel:

```
sudo snap install microk8s --channel=latest/edge/strict
```

Install the snap carrying the configuration. In this case the snap is `content-demo-microk8s`:

```
sudo snap install content-demo-microk8s --channel=latest/edge
```

Connect the configuration interface:

```
sudo snap connect content-demo-microk8s:configuration microk8s
```

After the configuration is applied, disconnect the interface:

```
sudo snap disconnect microk8s:configuration content-demo-microk8s:configuration
```

## Implementation

The end goal of this snap is to deliver a configuration yaml file to a MicroK8s cluster. To do so it implements
the slot side of a content interface:

```
slots:
  configuration:
    interface: content
    content: configuration
    source:
      read:
        - $SNAP/launcher
```

On the other end of the content interface (the plug) a MicroK8s instance will search for the configuration yaml file and apply
the requests setup.

## Links

- [MicroK8s.io](https://microk8s.io)
- [MicroK8s repository](https://github.com/canonical/microk8s)
- [Content interface](https://snapcraft.io/docs/content-interface)
