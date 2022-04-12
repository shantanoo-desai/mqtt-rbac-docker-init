# Role-Based Access Control in Eclipse Mosquitto MQTT Broker using Docker Init Containers

Mosquitto MQTT Broker 2.0 with Dynamic Security Plugin for Role-Based Access Control using Docker Compose Init Containers

## Images

| Name | Version | Purpose |
|:-----|:--------|:--------|
|`eclipse-mosquitto` | `2.0.14` | MQTT v3.1.1/v5 Broker |
| `cedalo/management-center` | `2.3.4` | Web-based UI for RBAC fine-tuning |

## Docker Init Container in Compose

> NOTE: this feature is similar to [Kubernetes Init Containers][1], which is available for
> Docker Compose since version 1.29.

The Init Container can initialize your container by using the `depends_on` spec. Depending on the
intialization process, you can set three conditions of the container's state you wish to initialize:

- `service_started`
- `service_healthy`
- `service_completed_sucessfully`

Unfortunately, this feature is yet to be documented. However, some resources to look into:

1. [Feature Request Issue on Docker Compose ][2]
2. [Pending Pull-Request for Documentation of Init Containers][3]
3. [StackExchange Query with a _possible_ example][4]

## Usage

Create an external docker network

```bash
docker network create proxy-network
```

```bash
docker compose up
```
You will see that `init-mqtt` service _bootstraps_ the `mqtt` service by:

- generating the `dynamic-security.json` file in the `init-mqtt` container
- the JSON file is available to `mqtt` service via the `mqtt-dynsec` shared volume

```bash
docker compose down --volumes # purge the created JSON file if you want to restart again
```

### Availability

| Service | Port |
| MQTT Broker | `1883` |
| MQTT management Center | `8088` |

### Dynamic Security RBAC control

1. Login into `http://localhost:8088` (see credentials in `cedalo.env`)
2. Click on Top-Left Icon to expand the Options

![Dynamic Security Plugin UI Snap](./.github/assets/cedalo_main_dyn_sec_1.png)

3. Click on Clients to generate new users that can Publish / Subscribe to the Broker 

> You will have to go through the [Dynamic Security Plugin Documentation from Mosquitto][5]
> in order to understand what __Clients__, __Groups__, __Roles__ are for fine granularity on
> MQTT Topics using RBAC mechanism.


## System Specs

Docker Engine version

```bash
Client:
 Version:           20.10.12
 API version:       1.41
 Go version:        go1.17.5
 Git commit:        e91ed5707e
 Built:             Mon Dec 13 22:31:40 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server:
 Engine:
  Version:          20.10.12
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.17.5
  Git commit:       459d0dfbbb
  Built:            Mon Dec 13 22:30:43 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          v1.6.1
  GitCommit:        10f428dac7cec44c864e1b830a4623af27a9fc70.m
 runc:
  Version:          1.1.0
  GitCommit:        v1.1.0-0-g067aaf85
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```

Docker Compose CLI Version (part of `docker` CLI)

```bash
Docker Compose version 2.3.3
```

## Additional Resources

[Steve's Internet Guide on MQTT Dynamic Security][6]


[1]: https://kubernetes.io/docs/concepts/workloads/pods/init-containers/
[2]: https://github.com/docker/compose/issues/6855
[3]: https://github.com/docker/docker.github.io/issues/12633
[4]: https://stackoverflow.com/questions/70322031/does-docker-compose-support-init-container
[5]: https://mosquitto.org/documentation/dynamic-security/
[6]: http://www.steves-internet-guide.com/understanding-mosquitto-dynamic-security-plugin/