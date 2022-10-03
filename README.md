# microk8s-content-demo-snap
Snap showing how to use the configuration launcher in MicroK8s.

In this snap you will find a yaml manifest with the configuration expected by a strictly confined MicroK8s deployment.
Through this configuration you can currently only enable and disable but more functionality will be added soon.

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
sudo snap connect microk8s:configuration content-demo-microk8s:configuration
```

After the configuration is applied, disconnect the interface:

```
sudo snap disconnect microk8s:configuration  content-demo-microk8s:configuration
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
        - $SNAP/configuration
```

On the other end of the content interface (the plug) a MicroK8s instance will search for the configuration yaml file and apply
the requests setup.

Both sides of the contect interface need to agree on a configuration with the same schema. The demo configuration included
in the current version of the snap only handles the enablement of addons:

```
addons:
  - name: dns
    status: enable
  - name: ingress
    status: disable
```


## Links

- [MicroK8s.io](https://microk8s.io)
- [MicroK8s repository](https://github.com/canonical/microk8s)
- [Content interface](https://snapcraft.io/docs/content-interface)
