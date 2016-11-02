# DCOS GeoServer Cluster

Complete bootstrapping of clustered GeoServer in a DCOS environment.

This repository contains scripts responsible both for initialization and synchronization of multiple GeoServer instances
running in Mesosphere DCOS. Synchronization is done by watching the GeoServer data directory for changes and then
signaling each GeoServer to reload the configuration from the shared data directory. This configuration reload is
performed in round robin fashion on a configurable interval. 

## Usage

From the DCOS Administrative Web UI browse to the Universe package page, select the GeoServer package and click Install.
If marathon-lb is not already installed, it should also be installed from the Universe package page.
Within a minute the _geoserver_ and _geoserver-app_ applications should be active and healthy. Once it is healthy
simply browse to the URL specified in the haproxy-vhost label of the _geoserver-app_ application in Marathon. This
defaults to geoserver.marathon.mesos unless overridden with the advanced install settings.
  
## Environment Variables

The following are variables that are either used during bootstrapping of the _geoserver-app_ Marathon application
or to tune the synchronization of the instances on configuration update.
 
### Synchronization Tuning
* FILE_BLACKLIST: comma delimited list of files to ignore during file system polling (.log)
* GEOSERVER_DATA_DIR: file system location to watch for updates
* GOSU_USER: User ID and group ID (user:group) ownership of GeoServer configuration storage. (default of _root:root_)
* GS_RELOAD_INTERVAL: time in seconds between reload of each instance
* GS_PROTOCOL: protocol prefix, should be set to 'http' or 'https'
* GS_RELATIVE_URL: relative URL to GeoServer REST API
* MARATHON_ROOT_URL: protocal, address or ip and port to Marathon (default of 
['http://marathon.mesos:8080','https://marathon.mesos:8443'])
* MARATHON_APP_PORT: internal port of service (internal to docker container: default of 8080)
* POLLING_INTERVAL: time in seconds between file system poll for configuration updates

### Bootstrap Tuning
* AUTH_URI: URI to a .dockercfg file used to pull from private registry (no default)
* DCOS_OAUTH_TOKEN: Auth token for deployment of GeoServer instances in DCOS EE (defaults to '')
* DCOS_PACKAGE_FRAMEWORK_NAME: Value passed through from DCOS package (defaults to geoserver)
* ENABLE_CORS: Boolean indicating whether GeoServer image should add response headers for CORS (default is false)
* GEOSERVER_INSTANCES: Number of server instances in cluster (defaults to 3)
* GEOSERVER_CPUS: CPU cores alloted to each GeoServer instance (defaults to 2)
* GEOSERVER_MEMORY: Memory alloted to each GeoServer instance (defaults to 512MiB)
* GEOSERVER_IMAGE: Docker image used for the GeoServer instance (defaults to appliedis/geoserver:2.8)
* HAPROXY_VHOST: Public address for access to the GeoServer cluster (defaults to geoserver.marathon.mesos). 
Multiple values are allowed in comma delimited form (http://public1,http://public2). 
* HAPROXY_PORT: Port bound on marathon-lb for the services (defaults to 8080)
* HOST_GEOSERVER_DATA_DIR: location the GeoServer data directory resides on the host (defaults to /shared/geoserver)
* HOST_SUPPLEMENTAL_DATA_DIRS: comma separated locations of data stored on host to mount RO into container (no default)