---
layout: single
toc: true   
tags: DevSecOps Jenkins Security Secret Management
---

Jenkins is an open source automation tool that provides support for build and deployment. With hundreds of plugins Jenkins provides a lot of customization and extensibiliy.

> If you are new to Jenkins you can quickly try it out by running its docker image:
>
> `docker run -p 8080:8080 jenkins/jenkins`

## CI/CD

Automation is fundamental to *Continuous Integration* and *Countinuous Delivery*. Jenkins build can be setup for Continuous Integration tasks such as running integration testing. And Continuous Delivery activities such as building a final jar and pushing it to a repository or building a Docker image and pushing it to a registry. However all such integrations require credentials for authorization. Applications tend have a lot of other secrets such as Certificates for authentication for [MASSL](https://en.wikipedia.org/wiki/Mutual_authentication), credentials for upstream systems, databases and API tokens.

## Jenkins Plugins for Secret Management

Following plugins allow basic credential management:
* *[Credentials Binding Plugin](https://wiki.jenkins.io/display/JENKINS/Credentials+Binding+Plugin)*: Allows credentials to be bound to environment variables for use from miscellaneous build steps.
* *[Credentials Plugin](https://wiki.jenkins.io/display/JENKINS/Credentials+Plugin)*: This plugin allows you to store credentials in Jenkins.
These plugins are available in default installation of Jenkins.

A number of secret formats are supported out-of-box:    
![](/assets/images/storing-secrets-in-jenkins/default_plugin.png "Default plugin secret options")    

### Setting up Secrets in Freestyle Pipeline

For example, let's create a Free style Project and use Credentials plugins to store Dockerhub credentials. Select _Username and password (separated)_ and enter Dockerhub credentials:

![](/assets/images/storing-secrets-in-jenkins/dockerhub_credentials.png "Enter Dockerhub credentials")

Use these credentials

![](/assets/images/storing-secrets-in-jenkins/use_secrets.png "use credentials")
