# Nexus Platform with Docker Support, behind Nginx

This is a template for deploying Nexus Repository Manager and IQ Server behind an NGINX proxy to offload SSL using Docker Compose. Additional itegrations are also in this reference but are be commented out to simplify the initial experience.

I also add a few aliases to my /etc/hosts file to simulate DNS from outside of docker host but the apps are accessible over http wtihout them. If you're on Windows the file is here, c:\windows\system32\drivers\etc\hosts.

```
127.0.0.1      localhost iq-server.mycompany.com repo.mycompany.com registry.mycompany.com jira
```

## Operations

In addition to Docker Desktop, I recommend installing Kitematic to help with managing containers and seeing into logs. https://github.com/docker/kitematic/releases

I run Jenkins outside of Docker (local app) which allow it to hit the Nexus repo through Nginx so Docker repos work. Jenkins is also in the docker-compose file but commented out for now. It is set to use the same jenkins work folder so you can even switch back and forth.

The demo-setup script is a one time script to config docker and npm within NXRM; prior to running, review the docker-compose file and the persistent volume mounts. They are set to work on a linux machine and will need to be changed for a windows based machine. If you look at the script you can see it starts the environmetn with a docker-compose up -d and then creates and runs a few setup scripts once NXRM is responding to traffic.
```
./demo-setup.sh
```

To stop, use docker-compose:

```
docker-compose down
```

Subsequent runs can use docker-compose and don't the demo-setup.sh :

```
docker-compose up -d
```

## URL's

- Nexus Web UI with SSL accessible via https://repo.mycompany.com
- Nexus Web UI over http via http://localhost:8081
- Docker proxy group registry accessible via https://registry.mycompany.com
- Docker Private Registry accessible via https://registry.mycompany.com:5000  (docker push, not browser)
- IQ Server accessible via http://localhost:8070 or https://iq-server.mycompany.com

## Persistent Volumes

I've create a convention of putting all of the persistent volumes is a hidden folder in the my home folder
```
~/.demo-pv
  + /iq-data
  + /iq-logs
  + /nexus-data
```
It's not clear to me how these work on a windows machine but check your settings for shared drives in Docker Settings. For info check out Getting Started at: https://docs.docker.com/docker-for-windows/

## IQ Server Configuration

There is a config.yml in the IQ-Server folder which is where you can customize the config to work with a SMTP, JIRA and Proxy servers.

## SSL Certificates

The Ngnix docker image build process generates insecure SSL certificates with fake location information and CNAME of localhost. Understand the risks of using these SSL certificates before proceeding. A deployed solution should use a valid CA certificate.


## Dynamic Configuration

Working examples of how to provision a new blobstore and create docker repos using it are in here and part of the setup. There are also placeholders for other formats like Maven, nnpm, etc...

## Advanced

There are additional services defined within the docker-compose file but commented out to ease getting started. In addition to Jenkins, that weas mentioned early there are also services defined for Victoria, Clare, Anchore, JIRA, and the Webhook Listener.
