[![Dependency Status](https://www.versioneye.com/user/projects/56d6ba3dfa908e000e348ffc/badge.svg?style=flat)](https://www.versioneye.com/user/projects/56d6ba3dfa908e000e348ffc)

# maven-indexer

This Maven project is using
[org.apache.maven.indexer/indexer-core](https://www.versioneye.com/java/org.apache.maven.indexer:indexer-core/5.1.1)
to fetch and read maven indexes. Unfortunately the indexer-core project still relies on
Maven 3.0.5. With higher versions of Maven the indexer-core is not running correctly.

This project is only fetching and reading Maven indexes from different repository servers.
The project checks if the artefact is already in the [VersionEye](https://www.versioneye.com)
DB or not. If the artefact
is a new one it sends a message to the RabbitMQ server with the corresponding coordiantes.
There are different RabbitMQ workers running on Maven 3.3.X withe Eclipse Aether,
fetching and parsing the actual pom file and writing the new artefact to the VersionEye DB.

## Start the backend services for VersionEye

This project contains a [docker-compose.yml](docker-compose.yml) file which describes the backend services
of VersionEye. You can start the backend services like this:

```
docker-compose up -d
```

That will start:

 - MongoDB
 - RabbitMQ
 - ElasticSearch
 - Memcached

For persistence you should comment in and adjust the mount volumes in [docker-compose.yml](docker-compose.yml)
for MongoDB and ElasticSearch. If you are not interested in persisting the data on your host you can
let it untouched.

Shutting down the backend services works like this:

```
docker-compose down
```

## MongoDB Config

As primary database we are using MongoDB. To make this project work you need to configure
the MongoDB connection in `src/main/resources/mongo.properties`. If you run MongoDB as a
single instance, only fill out the first 3 lines.

## RabbitMQ Config

To configure the RabbitMQ connection adjust the settings in `srm/main/resources/settings.properties`.

## Maven Index Directory Config

The Maven Indexer is downloading the maven index to a local directory. The working directory for
that can be configured here: `srm/main/resources/settings.properties`.

## Dependencies

This project relies on versioneye_persistence and versioneye_service. These projects are currently
located in the [crawl_j](https://github.com/versioneye/crawl_j) project. Run

```
mvn install
```

on the crawl_j project to install the dependencies.

## Run

To start the crawler for Maven Central, run this command with Maven 3.0.5:

```
mvn crawl:central
```

This will fetch the maven index form Maven Central and iterate through it. If it finds an
Artifact which is not yet in the MongoDB, it will send a message to RabbitMQ. To make this
fully work it is required that at least 1 RabbitMQ consumer is running which is processing
the message from this project. The code for the consumers are located in the `versioneye/crawl_j`
project.

This command will fail if you run it with a Maven version higher than 3.0.5!

## Support

For commercial support send an email to `support@versioneye.com`.

## License

this project is licensed under the MIT license!

Copyright (c) 2016 VersionEye GmbH

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
