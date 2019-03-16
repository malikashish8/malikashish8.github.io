---
layout: single
classes: wide
toc: false
author_profile: false
toc_label: "Contents"      
tags: DevSecOps Jenkins Security WiP
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

For example, let's create a Free style Project and use Credentials plugins to store Dockerhub credentials. Select _Username and password (separated)_ and enter bindings for the credentials. These are the environment variables that username and password will be available as:

![](/assets/images/storing-secrets-in-jenkins/bindings.png "Enter bindings")

Next click on *Add* and select the right scope and enter Dockerhub credentials:

![](/assets/images/storing-secrets-in-jenkins/dockerhub_credentials.png "Enter Dockerhub credentials")

Use these credentials

![](/assets/images/storing-secrets-in-jenkins/use_secrets.png "use credentials")

### Issues with Hardcoding Credentials

It is managable to embed credentials in the build when you are solely managing a few builds. However, such an approach is not optimal when a team of users work on a number of build pipelines. Problems such as credential sharing, credential rotation and automation of builds call for a better approach to credential management. This is solved by a number of credential management products such as [Hashicorp Vault](https://www.vaultproject.io/), [Cyberark Vault](https://www.cyberark.com/products/privileged-account-security-solution/enterprise-password-vault/) and [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/). Some open source products like [CredStash](https://github.com/fugue/credstash) for AWS help with credential management.
