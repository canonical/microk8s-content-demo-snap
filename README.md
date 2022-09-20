# microk8s-content-demo-snap
Snap showing how to use the configuration launcher in MicroK8s.

In this snap you will find a yaml manifest with the configuration expected by a strictly confined MicroK8s deployment.
Through this configuration you can currently only enable and disable but more functionality will be added soon.

## Usage
Install microk8s from a strict channel:

```
sudo snap install microk8s --channel=1.25-strict
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


## Links

- [MicroK8s.io](https://microk8s.io)
- [MicroK8s repository](https://github.com/canonical/microk8s)
