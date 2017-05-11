# WildFly Docker image

This is an Dockerfile with [WildFly application server 10.1.0.Final](http://wildfly.org/).

## Build Docker

To build the docker you must launch the following command.

    docker build -t rudaondo/wildfly 
	
Behind the proxy.

    docker build --build-arg HTTP_PROXY=http://user:passwd:user:passwd@ProxyIP:ProxyPort --build-arg HTTPS_PROXY=http://user:passwd:user:passwd@ProxyIP:ProxyPort -t rudaondo/wildfly 
	
## Usage

To boot in standalone mode with admin console available remotely

    docker run -p 8080:8080 -p 9990:9990 rudaondo/wildfly

## Administration Console

Administration console will be available on the port `9990` of the container. The admin user is `wildflyadmin` and the key `wildflypassword`.

## Application deployment

With the WildFly server you can [deploy your application in multiple ways](https://docs.jboss.org/author/display/WFLY8/Application+deployment):

1. You can use CLI
2. You can use the web console
3. You can use the management API directly
4. You can use the deployment scanner

The most popular way of deploying an application is using the deployment scanner. In WildFly this method is enabled by default and the only thing you need to do is to place your application inside of the `deployments/` directory. It can be `/opt/jboss/wildfly/standalone/deployments/` or `/opt/jboss/wildfly/domain/deployments/` depending on [which mode](https://docs.jboss.org/author/display/WFLY8/Operating+modes) you choose (standalone is default in the `jboss/wildfly` image -- see above).

The simplest and cleanest way to deploy an application to WildFly running in a container started from the `jboss/wildfly` image is to use the deployment scanner method mentioned above.

To do this you just need to extend the `jboss/wildfly` image by creating a new one. Place your application inside the `deployments/` directory with the `ADD` command (but make sure to include the trailing slash on the deployment folder path, [more info](https://docs.docker.com/reference/builder/#add)). You can also do the changes to the configuration (if any) as additional steps (`RUN` command).  

[A simple example](https://github.com/goldmann/wildfly-docker-deployment-example) was prepared to show how to do it, but the steps are following:

1. Create `Dockerfile` with following content:

        FROM rudaondo/wildfly
        ADD your-awesome-app.war /opt/jboss/wildfly/standalone/deployments/
2. Place your `your-awesome-app.war` file in the same directory as your `Dockerfile`.
3. Run the build with `docker build --tag=wildfly-app .`
4. Run the container with `docker run -it wildfly-app`. Application will be deployed on the container boot.

This way of deployment is great because of a few things:

1. It utilizes Docker as the build tool providing stable builds
2. Rebuilding image this way is very fast (once again: Docker)
3. You only need to do changes to the base WildFly image that are required to run your application

Administration console will be available on the port `9990` of the container.

