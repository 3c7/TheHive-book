# TheHive![](/assets/thehive-logo.png)Introduction

TheHive is a scalable 3-in-1 open source and free security incident response platform designed to make life easier for SOCs, CSIRTs, CERTs and any information security practitioner dealing with security incidents that need to be investigated and acted upon swiftly.

The code can be found on [Github](https://github.com/CERT-BDF/TheHive/).

## Hardware Pre-requisites

TheHive uses ElasticSearch to store data. Both software use a Java VM. We recommend using a virtual machine with 8vCPU, 8 GB of RAM and 60 GB of disk. You can also use a physical machine with similar specifications.

## What's New?

In general, latest news around the project can be found on the [project blog](https://blog.thehive-project.org).

## Installation Guides

TheHive can be [installed ](#installing-thehive)using:

* An [RPM package](#via-rpm)
* A [DEB package](#via-deb)
* Docker
* Binary
* Ansible script contributed by @drewstinnett 

TheHive can also be built from sources.

## Administration Guides

* Administrator's guide
* Configuration guide
* Updating
* Backup & Restore

## Developer Guides

* API documentation

## Other

* FAQ
* Training Material

## License

TheHive is an open source and free software released under the [AGPL](https://github.com/TheHive-Project/TheHive/blob/master/LICENSE) \(Affero General Public License\). We, TheHive Project, are committed to ensure that TheHive will remain a free and open source project on the long-run.

## Updates

Information, news and updates are regularly posted on [TheHive Project Twitter account](https://twitter.com/thehive_project) and on [the blog](https://blog.thehive-project.org/).

## Contributing

We welcome your contributions. Please feel free to fork the code, play with it, make some patches and send us pull requests using [issues](https://github.com/TheHive-Project/TheHive/issues).

We do have a [Code of conduct](https://github.com/TheHive-Project/TheHiveDocs/blob/master/code_of_conduct.md). Make sure to check it out before contributing.

## Support

Please [open an issue on GitHub](https://github.com/TheHive-Project/TheHive/issues) if you'd like to report a bug or request a feature. We are also available on [Gitter](https://gitter.im/TheHive-Project/TheHive) to help you out.

If you need to contact the Project's team, send an email to [support@thehive-project.org](mailto:support@thehive-project.org).

**Important Note**:

* If you have problems with [TheHive4py](https://github.com/TheHive-Project/TheHive4py), please [open an issue on its dedicated repository](https://github.com/TheHive-Project/TheHive4py/issues/new).
* If you encounter an issue with Cortex or would like to request a Cortex-related feature, please [open an issue on its dedicated GitHub repository](https://github.com/TheHive-Project/Cortex/issues/new).
* If you have troubles with a Cortex analyzer or would like to request a new one or an improvement to an existing analyzer, please open an issue on the [analyzers' dedicated GitHub repository](https://github.com/TheHive-Project/cortex-analyzers/issues/new).

## Community Discussions

We have set up a Google forum at [https://groups.google.com/a/thehive-project.org/d/forum/users](https://groups.google.com/a/thehive-project.org/d/forum/users). To request access, you need a Google account. You may create one [using a Gmail address](https://accounts.google.com/SignUp?hl=en) or [without it](https://accounts.google.com/SignUpWithoutGmail?hl=en).

## Website

https://thehive-project.org

# Installing TheHive

## Via RPM

TheHive's RPM packages are published on our Bintray repository. All packages are PGP signed using the key which ID is [562CBC1C](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY). The key's fingerprint is:

`0CD5 AC59 DE5C 5A8E 0EE1 3849 3D99 BB18 562C BC1C`

To intall TheHive from an RPM package, you'll need to begin by installing the RPM release package using the following command:

```
yum install https://dl.bintray.com/cert-bdf/rpm/thehive-project-release-1.0.0-3.noarch.rpm
```

This will install TheHive Project's repository in `/etc/yum.repos.d/thehive-rpm.repo` and the GPG public key `in /etc/pki/rpm-gpg/GPG-TheHive-Project`.

Once done, you will able to install TheHive package using yum:

```
yum install thehive
```

One installed, you should [install ElasticSearch](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/elasticsearch-guide.md) and [configure TheHive](https://github.com/TheHive-Project/TheHiveDocs/blob/master/admin/configuration.md).

## Via DEB

Debian packages are published on Bintray repository. All packages are signed using the key [562CBC1C](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY) \(fingerprint: `0CD5 AC59 DE5C 5A8E 0EE1 3849 3D99 BB18 562C BC1C`\):

```
echo 'deb https://dl.bintray.com/cert-bdf/debian any main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-key adv --keyserver hkp://pgp.mit.edu --recv-key 562CBC1C
sudo apt-get update
sudo apt-get install thehive
```

If the command `sudo apt-key adv --keyserver hkp://pgp.mit.edu --recv-key 562CBC1C` fails \(because your infrastructure refuses the acccess to pgp.mit.edu server key\), you can run with the equivalent command: `curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -`

After package installation, you should install ElasticSearch \(see [ElasticSearch installation guide](https://github.com/TheHive-Project/TheHiveDocs/blob/master/installation/elasticsearch-guide.md)\) and configure TheHive \(see [configuration guide](https://github.com/TheHive-Project/TheHiveDocs/blob/master/admin/configuration.md)\).

## Via Docker

Starting from version 2.11 \(Mellifera\), TheHive Docker image doesn't come with ElasticSearch. As TheHive requires it to work, you can:

* use docker-compose
* manually install and configure ElasticSearch

### Use `docker-compose`

Docker-compose can start multiple dockers and link them together. It can be installed using the [documentation](https://docs.docker.com/compose/install/). The following [docker-compose.yml](https://raw.githubusercontent.com/TheHive-Project/TheHive/master/docker/thehive/docker-compose.yml) file starts ElasticSearch, Cortex and TheHive:

```
version: "2"
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:5.5.2
    environment:
    	- http.host=0.0.0.0
    	- transport.host=0.0.0.0
    	- xpack.security.enabled=false
    	- cluster.name=hive
      - script.inline=true
      - thread_pool.index.queue_size=100000
      - thread_pool.search.queue_size=100000
      - thread_pool.bulk.queue_size=100000
  cortex:
    image: certbdf/cortex:latest
    ports:
      - "0.0.0.0:9001:9000"
  thehive:
    image: certbdf/thehive:latest
    depends_on:
      - elasticsearch
      - cortex
    ports:
      - "0.0.0.0:9000:9000"
```

Put this file in an empty folder and run `docker-compose up`. TheHive is exposed on 9000/tcp port and Cortex on 9001/tcp. These ports can be changed by modifying the `docker-compose` file.

You can specify a custom `application.conf` file by adding the lines, in `thehive` section:

```
volumes:
    - /path/to/application.conf:/etc/thehive/application.conf
```

You should define where the data \(i.e. the ElasticSearch database\) will be stored in your server by adding the lines, in `elasticsearch` section:

```
volumes:
    - /path/to/data:/usr/share/elasticsearch/data
```

### Manual installation of Elastic Search

ElasticSearch can be installed on the same server as TheHive or on a different one. You can then configure TheHive according to the [documentation](https://github.com/TheHive-Project/TheHiveDocs/blob/master/admin/configuration.md) and run TheHive docker as follow:

```
docker run --volume /path/to/thehive/application.conf:/etc/thehive/application.conf certbdf/thehive:latest --no-config
```

You can add the `--publish` docker option to expose TheHive HTTP service.

### Customizing the Docker image

By Default, TheHive docker image adds minimal configuration:

* choose a random secret \(play.crypto.secret\)
* search ElasticSearch instance \(host named 
  `elasticsearch`
  \) and add it to configuration
* search Cortex instance \(host named 
  `cortex`
  \) and add it to configuration

This behavior can be disabled by adding `--no-config` to the docker command line: `docker run certbdf/thehive:latest --no-config` or by adding the line `command: --no-config` in `thehive` section of docker-compose file.

The image accepts more options:

* --no-config : do not try to configure TheHive \(add secret and elasticsearch\)
* --no-config-secret : do not add random secret to configuration
* --no-config-es : do not add elasticsearch hosts to configuration
* --es-hosts : use this string to configure elasticsearch hosts \(format: \["host1:9300","host2:9300"\]\)
* --es-hostname : resolve this hostname to find elasticseach instances
* --secret : secret to secure sessions
* --cortex-proto : define protocol to connect to Cortex \(default: http\)
* --cortex-port : define port to connect to Cortex \(default: 9000\)
* --cortex-url : add Cortex connection
* --cortex-hostname : resolve this hostname to find Cortex instances

Please remember that you must install and configure ElasticSearch.

### How to use the Docker image

Once you have installed and configured ElasticSearch as shown above, the easiest way to start Cortex is the following one:

```
docker run certbdf/thehive
```



