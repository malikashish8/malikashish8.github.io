---
layout: single
classes: wide
toc: true
author_profile: false
toc_label: "Contents"      
tags: DevSecOps Jenkins Security
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

Use these credentials:

![](/assets/images/storing-secrets-in-jenkins/use_secrets.png "use credentials")

### Using Credentials in Jenkins Pipeline

Credentials can be similarly used in Jenkinsfile. In the following stage we are using `artifactory-credentials` to login to artifactory and push docker images to it. `usernameVariable` and `passwordVariable` are available in the `withCredentials` block:
```groovy
    stage("docker_push") {
      withCredentials([usernamePassword(credentialsId: 'artifactory-credentials', 
        passwordVariable: 'ARTIFACTORY_KEY', 
        usernameVariable: 'ARTIFACTORY_USER')]) 
      {
        sh "echo $ARTIFACTORY_KEY | docker login -u $ARTIFACTORY_USER --password-stdin ${REGISTRY_URL}"
        sh "docker tag myapp:latest ${REGISTRY_URL}/myapp:${shortCommit}"
        sh "docker push ${REGISTRY_URL}/myapp:${shortCommit}"
      } 
```
### Retrieving Secrets
Jenkins tries to provide some sence of security by masking the credentials in logs. It looks for an exact match and replaces them with asterisks (*****). But this control can simply be bypassed by a user with edit permission by encoding the credentials in the pipeline and printing them in logs.

![](/assets/images/storing-secrets-in-jenkins/encode_password.png)

![](/assets/images/storing-secrets-in-jenkins/password_encoded.png)

> Note that the first echo results in password being masked since it matches the password string.

Decode the encoded password:
```bash
dev@ubu:~ $ echo YWRtaW4xMjM0Cg== | base64 -d
admin1234
```
Jenkins credentials are stored in Jenkins master [encrypted by Jenkins instance id](https://jenkins.io/doc/book/using/using-credentials/#credential-security). This file generally exists at `/var/lib/jenkins/credentials.xml`. It is therefore trivial to decrypt credentials saved in Jenkins for Jenkins administrators. Moreover, any user that can SSH to Jenkins can read the encrypted credentials file since it has read permissions for everyone:
```bash
dev@ubu:/var/lib/jenkins$ ls -la credentials.xml 
-rw-r--r-- 1 jenkins jenkins 4650 Mar 26 09:28 credentials.xml
```
This file has passwords encrypted with Jenkins Id:
```xml
<com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl>
    <scope>GLOBAL</scope>
    <id>artifactory-credentials</id>
    <description></description>
    <username>admin</username>
    <password>{AQAAABAAAAAQom3LN7ei0wdm9cdOlGOa4GxDHzpndn0BUPeI4biARto=}</password>
</com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl>
```

Jenkins provides a handy utility at `/script` of the URL that can be used to decrypt passwords with the following script:
```
encryptedPassword = '{AQAAABAAAAAQom3LN7ei0wdm9cdOlGOa4GxDHzpndn0BUPeI4biARto=}'
passwd = hudson.util.Secret.decrypt(encryptedPassword)
println(passwd)
```
![](/assets/images/storing-secrets-in-jenkins/jenkins_script.png)

__Summing it up__, it is not safe to assume that secrets entered in Jenkins will not be disclosed. Any user that can use a secret in a build can decode and see it. All users that can SSH into Jenkins master node can decript all secrets using `/script` utility if they can login to Jenkins Web UI.
## Management Issues with Hardcoding Credentials

As we saw above, the principle of least privilege cannot be effectively employed when just using Jenkins Credentials Plugin. There has to be a better solution for auditable credential sharing within team, credential rotation and automatic build provisioning. This is solved by a number of credential management products such as [Hashicorp Vault](https://www.vaultproject.io/), [Cyberark Vault](https://www.cyberark.com/products/privileged-account-security-solution/enterprise-password-vault/) and [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/). Some open source products like [CredStash](https://github.com/fugue/credstash) for AWS help with credential management.
