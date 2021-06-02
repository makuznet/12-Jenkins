# Jenkins — Deployment automation

> This repo creates three VPS in Yandex.Cloud — one with Jenkins and two others with Docker — to learn how to write Jenkins jobs code and execute this code both locally and remotely.

## Usage
### Jenkins
#### Accessing Jenkins UI
```bash
http://217.28.231.237:8080
```
#### Other Jenkins configuration steps
1. Start with 'Manage Jenkins' menu.
    - Add user and id_rsa (private key) with 'Manage Credentials' menu; New node;
    - Add agents (as you call it: nodes, hosts, VPSes) to be able to execute jobs on that agents;
    > Add labels to agents: the more — the better :) It will allow you to choose
2. Add jobs; New Item; 
    - Freestyle project if you want to run a shell script one one agent;
    - Pipeline if you want to define agents;
    - Multibranch Pipeline if you want to apply one-git-branch-one-Jenkinsfile approach;    

Look at Multibranch Pipeline example here:
https://github.com/makuznet/12-Jenkinsfile-github-branches

#### Pipeline examples
> Pay attention that `agent none` and then `label 'staging'`. This is how you define what agent to run
```bash
pipeline {
    agent none
    stages {
        stage('docker wp start') {
            agent { 
                label 'staging'
            }
            steps {
                sh '''
                1st script line
                2nd script line
                3rd script line
                '''
            }
        }
    }
}
```
> Here you can define your job start period in `triggers`.
```bash
pipeline {
    agent none
    triggers {
        cron('H(0-59)/5 * * * *')
    }
    stages {
        stage('ping agents') {
            agent { 
                label 'master'
            }
            steps {
                sh 'one line'
            }
        }
    }
}
```
> Build with parameters
```bash
pipeline {
    agent none
    stages {
        stage('Setup parameters') {
            steps {
                script { 
                    properties([
                        parameters([
                            choice(
                                choices: ['STAGE', 'PROD', 'ALL'], 
                                name: 'REBOOT',
                                description: 'What agent to reboot now?'
                            )
                        ])
                    ])
                }
            }
        }
        stage('reboot staging') {
            agent { 
                label 'staging'
            }
            when {
                expression { 
                   return params.REBOOT == 'STAGE'
                }
            }
            steps {
                sh 'sudo shutdown -r'
            }
        }
```

### Run Wordpress on Staging VPS
```bash
http://217.28.229.81/wp-admin/install.php # first run
http://217.28.229.81/wp-login.php # admin panel
http://217.28.229.81 # web site
```
### Run Wordpress on Production VPS
```bash
http://217.28.229.157/wp-admin/install.php 
http://217.28.229.157/wp-login.php
http://217.28.229.157
```
## Installation
See Terraform files and Ansible roles for details.
 
### Jenkins
#### Accessing Jenkins UI
```bash
http://217.28.231.237:8080
```
#### What plug-ins to deselect when configuring Jenkins
- OWASP;
- Ant;
- Gradle;
- GitHub branch source;
- Pipeline: GitHub Groovy Libraries:
- Pipeline: Stage View;
- LDAP;

## Extra
### Docker
#### List running containers
```bash
sudo docker ps
```
#### List all containers
```bash
sudo docker container ls -a
```
#### Stop a container
> wordpressdb is the name of the container not an image !
```bash
sudo docker stop wordpressdb
```
#### Remove a container
```bash
sudo docker rm wordpressdb
```
### Masterclass video MoM
```bash
ansible-playbook --limit=nginx play.yml --check -vvv --diff

/var/lib/jenkins — home dir 
archive Jenkins by creating a Jenkins home dir tarball 

Workspace is a dir in Jenkins home dir 

set -x enables entered commands log

docker pull mysql:5.7

Add jenkins user to docker group

adduser jenkins docker

service jenkins restart
systemctl restart jenkins

Switch off authentication in Jenkins
vi config.xml
    useSecurity false


Job will return failure if exit 1 is added.
docker pull mysql:5.7
exit 1

curl ifconig.me returns your VPS IP address 


docker pull wordpress
docker run --rm -it -d -p 8000:8000 wordpress 

jenkins.mono-project.com

ansible configurations: '01:23:53 Sfactory module 12.mp4'
details: 01:32:17
```

## Acknowledgments

This repo was inspired by [skillfactory.ru](https://skillfactory.ru/devops#syllabus) team

## See Also
- [Yandex CLI](https://cloud.yandex.com/en-ru/docs/cli/quickstart)  
- [Terraform Yandex.Cloud Provider](https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs)  
- [Yandex Getting started with Terraform](https://cloud.yandex.com/en-ru/docs/solutions/infrastructure-management/terraform-quickstart)  
- [Yandex VM instance metadata](https://cloud.yandex.com/en-ru/docs/compute/concepts/vm-metadata)  
- [Using Jenkins agents](https://www.jenkins.io/doc/book/using/using-agents/)  
- [How to install WordPress with Docker](https://upcloud.com/community/tutorials/wordpress-with-docker/)  
- [multiple agent labels in a Jenkins pipeline](https://stackoverflow.com/questions/43321026/can-i-define-multiple-agent-labels-in-a-declarative-jenkins-pipeline)  
- [How to Use Parameters in Jenkins Declarative Pipeline](https://devopscube.com/declarative-pipeline-parameters/)  
- [Getting started with Pipeline](https://www.jenkins.io/doc/book/pipeline/getting-started/)  
- [Git for Jenkins projects](https://plugins.jenkins.io/git/)  

## License
Follow all involved parties licenses terms and conditions.