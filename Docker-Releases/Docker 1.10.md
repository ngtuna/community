# Docker 1.10

## Included releases
- Docker Engine 1.10 - https://docs.docker.com/engine/
- Docker Machine 0.6 - https://docs.docker.com/machine/
- Docker Compose 1.6 - https://docs.docker.com/compose/
- Docker Swarm 1.1 - https://docs.docker.com/swarm/
- Docker Registry 2.3 - https://docs.docker.com/registry/

**Key highlighted**
- end to end application orchestration with powerful app composition
- Comprehensive security for container applications
- Enhanced container networking capabilities

### New Docker compose file format v.2
```
version: “2”
services:
  web:
    image: docker/myapp 
    networks:
      - front-tier
      - back-tier
  redis:
    image: redis
    volumes:
      - data:/var/lib/redis
    networks:
      - back-tier
volumes:
  data:
    driver: local
networks:
  front-tier:
    driver: bridge
  back-tier:
    driver: bridge
```
*the new file format for a 2 service app using local volume and two networks*

Compose files used to describe just one thing: the services that make up your distributed application. We’ve now added **networks** and **volumes** to the mix, allowing you to describe much more complex applications. You can set up your app on your development machine with *multiple network tiers* and *complex storage configurations*, replicating how you might set it up in production. You can then take that same configuration from development, and use it to run your app on CI, on staging, and right through into production.

Check out release note for full list of what's new: https://github.com/docker/compose/releases/tag/1.6.0
Everything that used to be in a Compose file is now under a new `services` key.

####Upgrade file format from v.1 to v.2
https://docs.docker.com/compose/compose-file/#upgrading
If you’re not quite ready to make the jump yet, that’s fine – Compose 1.6 still works just fine with the old format.

### Docker Swarm
Enhanced reliablity and availability
- **reschedule containers when a node fails** (experimental): If a node fails, Swarm can now optionally attempt to reschedule that container on a healthy node to keep it running.
- **Better node management** If Swarm fails to connect to a node, it will now retry instead of just giving up. It will also display the status of this and any error messages in `docker info`, making it much easier to debug.  

### Docker Engine
- **Comprehensive container security**
- **Networking enhancements**
- **Tools for IT operation**

####Comprehensive container security
***User namespace***
- Configurable granular access control
- Configure separate privileges for containers and the Docker host
- Assign privileges for each container by user group

***Seccomp profiles***
- Granular policy control defining what syscalls containers can make
- Increase security by reducing attack surface of the Linux kernel
- Secure by default with "default profile" applied to all containers
- Syscalls like `acct`, `add_key`, `clone`, `kexec_load`, `mount`, `reboot` and `others` are blocked by default profile.
 
***Additional enhancements***
- Content addressable image IDs: Image IDs now represent the content that is inside an image, in a similar way to how Git commit hashes represent the content inside commits. This means you can guarantee that the content you're running is what you expect by just specifying that image's ID. This is an improvement upon the image digests in Engine 1.6.
- Temporary filesystems: it's now easy to create temporary filesystems by passing the `--tmpfs` flag to `docker run`. This is particularly useful for running a container with a read-only filesystem when the piece of software inside container expects to be able to write to certain locations on disk.
- Authorization plugins: Admin can configure granular access policies for managing access to Docker daemon. The plugins acts as interceptors that can allow or deny the Docker API request based off the rules created by admin. These plugins are installed and configured will work the same as current plugins for volume and networking via Docker plugin API.
 
####Networking enhancements
- Use `link` in custom networks: `Link` just worked in the default bridge network, but now you can use `link` in networks you created. So you can define the relationships between your containers and alias a hostname to a different name inside a specific container (ex: `--link db:production_postgres`)
- Network-wide container aliases: `Link` let you alias a hostname for a specific container, but you can now make a container accessible by multiple hostnames across an entire network.
- Internal networks: Pass the `--internal` flag to `network create` to restrict traffic in and out of the network
- Custom IP addresses: You can now give a container a custome IP address once running it or adding it to a network
- DNS server for name resolution: Hostname lookups are done with a DNS server rather than `etc/hosts`, making it much more reliable and scalable.
- Multi-host networking on all supported Engine kernel versions: The multi-host overlay driver now works on older kernel versions (3.10 and greater)

####Tools for IT operation
- Live update container resource constraits: Using `docker update` to set limits on what resources containers can use without restart the container.
- Daemon configuration file: it's now possible to configure daemon options in a file and reload some of them without restarting the deamon. Thus, for ex, you can set new daemon labels and enable debug logging without restarting anything.
- Constraints on disk I/O: various options for setting constraints on disk I/O have been added to `docker run`: `--device-read-bps`, `--device-write-bps`, `--device-read-iops`, `--device-write-iops`, `--blkio-weight-device`.
- `Splunk` logging driver: ship container logs straight to the Splunk logging service.
- Better event stream: `Docker events` command and events API endpoint has been improved and cleaned up. Events are now consistently structured with a resource type and the action being performed against that resource, and events have been added for actions against volumes and networks.
- Improved push/pull performance and reliability: Layers are now pushed in parallel, resulting in much faster pushes (3x faster). Pulls are a bit faster and more reliable too - with a streamlined protocol and better retry and fallback mechanisms.
- Start linked containers in correct order when restarting daemon: Before, it you restarted a daemon with linked containers, they sometimes failed to startup if the linked containers weren't running yet. Engine will now attempt to start up containers in the correct order.

### Slidedeck overview: https://docs.google.com/presentation/d/1nOoCDreO7U6jF7GS1KGUtkpVTlW4a61TQsh4ClcR4_Y/edit?usp=sharing
*feel free to use and modify these slides!*


---


Video on what's new in Docker 1.10: https://www.youtube.com/watch?v=OsOLF3_fotM


More about Compose 1.6: https://blog.docker.com/2016/02/compose-1-6/


Video on the new version of Docker Compose: https://www.youtube.com/watch?v=EReEOMS7gsk


More about Docker Engine 1.10 Security Enhancements: https://blog.docker.com/2016/02/docker-engine-1-10-security/


Video on best practices for building secure Docker images: https://www.youtube.com/watch?v=LmUw2H6JgJo
