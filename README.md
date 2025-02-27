
# Experience-Continuous-Integration-with-Jenkins-Ansible-Artifactory-SonarQube-PHP


```IMPORTANT NOTICE``` - This project has some initial theoretical concepts that must be well understood before moving on to the practical part. Please read it carefully as many times as needed to completely digest the most important and fundamental DevOps concepts. To successfully implement this project, it is crucial to grasp the importance of the entire CI/CD process, roles of each tool and success metrics - so we encourage you to thoroughly study the following theory until you feel comfortable explaining all the concepts (e.g., to your new junior colleague or during a job interview).

In previous projects, We have been deploying a tooling website directly into the /var/www/html directory on dev servers. Well, even though that worked fine, and we were able to access the website, it is not the best way to do it. Real world web application code written on Java, .NET or other compiled programming languages require a build stage to create an executable file. The executable file (e.g., jar file in case of Java) contains all the codes embedded, and the necessary library dependencies, which the application needs to run and work successfully.

Some other programs written languages such as PHP, JavaScript or Python work directly without being built into an executable file - these languages are called ``interpreted``. That is why we could easily deploy the entire code from git into var/www/html and immediately the webserver was able to render the pages in a browser. However, it is not optimal to download code directly from Git onto our servers. There is a smarter way to package the entire application code, and track release versions. We can package the entire code and all its dependencies into an archive such as .tar.gz or .zip, so that it can be easily unpacked on a respective environment's servers.

In this project, you will understand and get hands on experience around the entire concept around CI/CD from applications perspective. To fully gain real expertise around this idea, it is best to see it in action across different programming languages and from the platform perspective too. From the application perspective, we will be focusing on PHP here.

## What is Continuous Integration?


Continuous Integration (CI) is a practice of merging all developers' working copies to a shared mainline (e.g., Git Repository or some other version control system) several times per day. Frequent merges reduce chances of any conflicts in code and allow to run tests more often to avoid massive rework if something goes wrong. This principle can be formulated as Commit early, push often.



The general idea behind multiple commits is to avoid what is generally considered as Merge Hell or Integration hell. When a new developer joins a new project, he or she must create a copy of the main codebase by starting a new feature branch from the mainline to develop his own features (in some organization or team, this could be called a develop, main or master branch). If there are tens of developers working on the same project, they will all have their own branches created from mainline at different points in time. Once they make a copy of the repository it starts drifting away from the mainline with every new merge of other developers' codes. If this lingers on for a very long time without reconciling the code, then this will cause a lot of code conflict or Merge Hell, as rightly said. Imagine such a hell from tens of developers or worse, hundreds. So, the best thing to do, is to continuously commit & push your code to the mainline. As many times as tens times per day. With this practice, We can avoid Merge Hell or Integration hell.


###  CI concept is not only about committing your code. There is a general workflow, let us start it.

- Run tests locally: Before developers commit their code to a central repository, it is recommended to test the code locally. So, Test-Driven Development (TDD) approach is commonly used in combination with CI. Developers write tests for their code called unit-tests, and before they commit their work, they run their tests locally. This practice helps a team to avoid having one developer's work-in-progress code from breaking other developers' copy of the codebase.

- Compile code in CI: After testing codes locally, developers commit and push their work to a central repository. Rather than building the code into an executable locally, a dedicated CI server picks up the code and runs the build there. In this project we will use, already familiar to you, Jenkins as our CI server. Build happens either periodically - by polling the repository at some configured schedule, or after every commit. Having a CI server where builds run is a good practice for a team, as everyone has visibility into each commit and its corresponding builds.

- Run further tests in CI: Even though tests have been run locally by developers, it is important to run the unit-tests on the CI server as well. But, rather than focusing solely on unit-tests, there are other kinds of tests and code analysis that can be run using CI server. These are extremely critical to determining the overall quality of code being developed, how it interacts with other developers' work, and how vulnerable it is to attacks. A CI server can use different tools for Static Code Analysis, Code Coverage Analysis, Code smells Analysis, and Compliance Analysis. In addition, it can run other types of tests such as Integration and Penetration tests. Other tasks performed by a CI server include production of code documentation from the source code and facilitate manual quality assurance (QA) testing processes.

- Deploy an artifact from CI: At this stage, the difference between CI and CD is spelt out. As you now know, CI is Continuous Integration, which is everything we have been discussing so far. CD on the other hand is Continuous Delivery which ensures that software checked into the mainline is always ready to be deployed to users. The deployment here is manually triggered after certain QA tasks are passed successfully. There is another CD known as Continuous Deployment which is also about deploying the software to the users, but rather than manual, it makes the entire process fully automated. Thus, Continuous Deployment is just one step ahead in automation than Continuous Delivery.


### Continuous Integration in The Real World.

To emphasize a typical CI Pipeline further, let us explore the diagram below a little deeper.

![image](https://github.com/user-attachments/assets/abc0c369-14fd-4266-a95b-4786afdbed8c)

- Version Control: This is the stage where developers' code gets committed and pushed after they have tested their work locally.

- Build: Depending on the type of language or technology used, we may need to build the codes into binary executable files (in case of compiled languages) or just package the codes together with all necessary dependencies into a deployable package (in case of interpreted languages).
- Unit Test: Unit tests that have been developed by the developers are tested. Depending on how the CI job is configured, the entire pipeline may fail if part of the tests fails, and developers will have to fix this failure before starting the pipeline again. A Job by the way, is a phase in the pipeline. Unit Test is a phase, therefore it can be considered a job on its own.
- deploy: Once the tests are passed, the next phase is to deploy the compiled or packaged code into an artifact repository. This is where all the various versions of code including the latest will be stored. The CI tool will have to pick up the code from this location to proceed with the remaining parts of the pipeline.
- Auto Test: Apart from Unit testing, there are many other kinds of tests that are required to analyse the quality of code and determine how vulnerable the software will be to external or internal attacks. These tests must be automated, and there can be multiple environments created to fulfil different test requirements. For example, a server dedicated for Integration Testing will have the code deployed there to conduct integration tests. Once that passes, there can be other sub-layers in the testing phase in which the code will be deployed to, so as to conduct further tests. Such are User Acceptance Testing (UAT), and another can be Penetration Testing. These servers will be named according to what they have been designed to do in those environments. A UAT server is generally be used for UAT, SIT server is for Systems Integration Testing, PEN Server is for Penetration Testing and they can be named whatever the naming style or convention in which the team is used. An environment does not necessarily have to reside on one single server. In most cases it might be a stack as you have defined in your Ansible Inventory. All the servers in the inventory/dev are considered as Dev Environment. The same goes for inventory/stage (Staging Environment) inventory/preprod (Pre-production environment), inventory/prod (Production environment), etc. So, it is all down to naming convention as agreed and used company or team wide.
- Deploy to production: Once all the tests have been conducted and either the release manager or whoever has the authority to authorize the release to the production server is happy, he gives green light to hit the deploy button to ship the release to production environment. This is an Ideal Continuous Delivery Pipeline. If the entire pipeline was automated and no human is required to manually give the Go decision, then this would be considered as Continuous Deployment. Because the cycle will be repeated, and every time there is a code commit and push, it causes the pipeline to trigger, and the loop continues over and over again.
- Measure and Validate: This is where live users are interacting with the application and feedback is being collected for further improvements and bug fixes. There are many metrics that must be determined and observed here. We will quickly go through 13 metrics that MUST be considered.

## Common Best Practices of CI/CD

Before we move on to observability metrics - let us list down the principles that define a reliable and robust CI/CD pipeline:

- Maintain a code repository.
- Automate build process.
- Make builds self-tested.
- Everyone commits to the baseline every day.
- Every commit to baseline should be built.
- Every bug-fix commit should come with a test case.
- Keep the build fast.
- Test in a clone of production environment.
- Make it easy to get the latest deliverables.
- Everyone can see the results of the latest build.
- Automate deployment (if you are confident enough in your CI/CD pipeline and willing to go for a fully automated Continuous Deployment).

### Why are we doing everything we are doing? - 13 DevOps Success Metrics.

- After all, DevOps is all about continuous delivery or deployment, and being able to ship out quality code as fast as possible. This is a very ambitious thing to desire; therefore, we must be careful not to break things as we are moving very fast. By tracking these metrics, we can determine our delivery speed and bottlenecks before breaking things.

- Ultimately, the goals of DevOps are enhanced Velocity, Quality, and Performance. But how do we track these parameters? Let us have a look at the 13 metrics to watch out for.

1. Deployment frequency: Tracking how often we do deployments is a good DevOps metric. Ultimately, the goal is to do more smaller deployments as often as possible. Reducing the size of deployments makes it easier to test and release. I would suggest counting both production and non-production deployments separately. How often you deploy to QA or pre-production environments is also important. You need to deploy early and often in QA to ensure enough time for testing.

2. Lead time: If the goal is to ship code quickly, this is a key DevOps metric. I would define lead time as the amount of time that occurs between starting on a work item until it is deployed. This helps you know that if you started on a new work item today, how long would it take on average until it gets to production.

3. Customer tickets: The best and worst indicator of application problems is customer support tickets and feedback. The last thing you want is your users reporting bugs or having problems with your software. Because of this, customer tickets also serve as a good indicator of application quality and performance problems.

4. Percentage of passed automated tests: To increase velocity, it is highly recommended that the development team makes extensive usage of unit and functional testing. Since DevOps relies heavily on automation, tracking how well automated tests work is a good DevOps metrics. It is good to know how often code changes break tests.

5. Defect escape rate: Do you know how many software defects are being found in production versus QA? If you want to ship code fast, you need to have confidence that you can find software defects before they get to production. Defect escape rate is a great DevOps metric to track how often those defects make it to production.

6. availability: The last thing we ever want is for our application to be down. Depending on the type of application and how we deploy it, we may have a little downtime as part of scheduled maintenance. It is highly recommended to track this metric and all unplanned outages. Most software companies build status pages to track this. Such as this Google Products Status Page.

7. Service level agreements: Most companies have some service level agreement (SLA) that they promise to the customers. It is also important to track compliance with SLAs. Even if there are no formally stated SLAs, there probably are application non-functional requirements or expectations to be met.

8. Failed deployments: We all hope this never happens, but how often do our deployments cause an outage or major issues for the users? Reversing a failed deployment is something we never want to do, but it is something you should always plan for. If you have issues with failed deployments, be sure to track this metric over time. This could also be seen as tracking *Mean Time To Failure (MTTF).

9. Error rates: Tracking error rates within the application is super important. Not only they serve as an indicator of quality problems, but also ongoing performance and uptime related issues. In software development, errors are also known as exceptions, and proper exception handling is critical. If they are not handled nicely, we can figure it out while monitoring the rate of errors.

    - Bugs – Identify new exceptions being thrown in the code after a deployment
    - Production issues – Capture issues with database connections, query timeouts, and other related issuesPresenting error rate metrics like this simply gives greater insights into where to focus attention.

10. Application usage & traffic: After a deployment, we want to see if the number of transactions or users accessing our system looks normal. If we suddenly have no traffic or a giant spike in traffic, something could be wrong. An attacker may be routing traffic elsewhere, or initiating a DDOS attack.

11. Application performance: Before we even perform a deployment, we should configure monitoring tools like Retrace, DataDog, New Relic, or AppDynamics to look for performance problems, hidden errors, and other issues. During and after the deployment, we should also look for any changes in overall application performance and establish some benchmarks to know when things deviate from the norm. It might be common after a deployment to see major changes in the usage of specific SQL queries, web service or HTTP calls, and other application dependencies. These monitoring tools can provide valuable visualizations like this one below that helps make it easy to spot problems.

12. Mean time to detection (MTTD): When problems happen, it is important that we identify them quickly. The last thing we want is to have a major partial or complete system outage and not know about it. Having robust application monitoring and good observability tools in place will help us detect issues quickly. Once they are detected, we also must fix them quickly!

13. Mean time to recovery (MTTR): This metric helps us track how long it takes to recover from failures. A key metric for the business is keeping failures to a minimum and being able to recover from them quickly. It is typically measured in hours and may refer to business hours, not calendar hours.


## Simulating a typical CI/CD Pipeline for a PHP Based application:


We will be tasked to create a pipeline that simulates continuous integration and delivery. Target end to end CI/CD pipeline is represented by the diagram below.

![image](https://github.com/user-attachments/assets/940c60e9-1ee7-46ca-a00f-2ac9b39612b0)

- It is important to know that both Tooling and TODO Web Applications are based on an interpreted (scripting) language (PHP).
- It means, it can be deployed directly onto a server and will work without compiling the code to a machine language.
- The problem with that approach is, it would be difficult to package and version the software for different releases.
- In this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible uri module.

### Set Up.

# Configure Ansible configuration repo For Jenkins Deployment

This is project is continuation from my ansible dynamic assignment projects. I would be using a copy of the ansible-config-mgt repo which I have renamed ansible-project.

![image](https://github.com/user-attachments/assets/efbf1328-54ba-48db-8de7-a9d2adb2e4c5)

## 1 Installing Jenkins.

Provision the Machine for jenkins:
![image](https://github.com/user-attachments/assets/61510651-2fa8-4b56-93d3-5a2805a42077)

![image](https://github.com/user-attachments/assets/dfe2b67b-4a90-4ba4-b5fe-7468a70a83ad)

Updating the machine
```
sudo apt update
```

Installing the java

```
sudo apt install default-jdk-headless
```

Installing jenkins through Official Website  https://pkg.jenkins.io/debian-stable/

This is the Debian package repository of Jenkins to automate installation and upgrade.
```
  sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

Then add a Jenkins apt repository entry:
```
      echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

```

Update your local package index, then finally install Jenkins:
```
  sudo apt-get update
  sudo apt-get install fontconfig openjdk-17-jre
  sudo apt-get install jenkins
```

Checking the Jenkins version.

```
jenkins --version
```

![image](https://github.com/user-attachments/assets/91099522-36ce-4066-b758-7b1dc08ea620)


## 2 Installing Blue-Ocean Plugin

To make managing your Jenkins pipelines easier and more intuitive, install the Blue Ocean plugin. This plugin offers a user-friendly and visually appealing interface, helping you quickly understand the status of your continuous delivery pipelines.

To do this , follow the steps below :
- Go to manage jenkins > manage plugins > available
- Search for BLUE OCEAN PLUGIN and install

![image](https://github.com/user-attachments/assets/fc36bb47-7844-49da-a2cc-7b45ed5cc650)

## 3 Configure the Blue Ocean plugin pipeline with our github repo.

To do this, follow the step below:

- Open the blue oceans plugin and create a new pipeline
- Select github.
- Connect github with jenkins using your github personal access token.
- Select the repository.
- Create the pipeline.

![image](https://github.com/user-attachments/assets/f9ddaae5-9088-4fa9-b91e-525db243f36a)

#### Login to GitHub & Generate an Access Token.

![image](https://github.com/user-attachments/assets/32dd6057-da94-465b-901d-ed4b1e53b464)

Paste Token on To Connect with github.
![image](https://github.com/user-attachments/assets/a998a545-fedb-48f2-bb70-d84fd8a0a206)


Selecting the ansible-project and creating the project.
![image](https://github.com/user-attachments/assets/e7ed79cb-a43f-4bb8-9c53-d7a6456ea3f6)


At this point you may not have a Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console.

![image](https://github.com/user-attachments/assets/e5442148-30a3-4b0b-8371-d60111a4c15f)

Let us create our Jenkinsfile

Inside the Ansible project, create a new directory deploy and start a new file Jenkinsfile inside the directory.

![image](https://github.com/user-attachments/assets/f75a3e4a-8d82-4919-8033-82aa5fb88956)


Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage


```
pipeline {

        agent any

        stages{
            stage ('Build') {
                steps {
                    sh 'echo "Building Stage"'
                }
            }

        }

}

```

Now go back into the Ansible pipeline in Jenkins, and select configure.

![image](https://github.com/user-attachments/assets/e7997b0a-b304-4b2a-84d4-0c2bf0c55fcd)

Scroll down to Build Configuration section and specify the location of the Jenkinsfile at deploy/Jenkinsfile.

![image](https://github.com/user-attachments/assets/139b1130-efab-4efc-a6ad-0549470360b0)

Back to the pipeline again, this time click "Build now"

![image](https://github.com/user-attachments/assets/51e592d9-30ab-412c-bae2-0cfa505f625f)

- Go back to the pipeline again, this time click Build now and then click on blue ocean on the right menu.

![image](https://github.com/user-attachments/assets/975deebe-642e-4a68-9068-143d6717954c)

![image](https://github.com/user-attachments/assets/b275383e-b686-45f6-8ed5-1813f003cfbc)

Let us see this in action.

1. Create a new git branch and name it feature/jenkinspipeline-stages


![image](https://github.com/user-attachments/assets/8c8a5a09-e4ed-4f5a-92e3-26ad0eafb4c1)

It will going to create the pipeline automatically.. We dont have to build and cofigure the pipeline again.
![image](https://github.com/user-attachments/assets/c4b5ea51-5f8c-4ccf-87fe-581d419e9d90)

2. Currently we only have the Build stage. Let us add another stage called Test. Paste the code snippet below and push the new changes to GitHub.

Add below code inside the JenkinsFile push code on remote repo and build it.
```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'echo "Testing Stage"'
                }
            }
        }
    }
}


```

![image](https://github.com/user-attachments/assets/22d528de-24e6-4be5-9cdc-aba4b04e800a)

Additinal Tasks to perform tp better understand the whole process.

- Let's create a pull request to merge the latest code into the main branch, after merging the PR, go back into your terminal and switch into the main branch.Pull the latest change.

- Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an echo command like we have in build and test stages)

1. Package.
2. Deploy.
3. Clean up.


Creating the new branch to add more stages...

![image](https://github.com/user-attachments/assets/5a003726-b485-42c0-a767-d1984bd48b14)

![image](https://github.com/user-attachments/assets/a1f0ebf0-0721-43d4-898d-1716887480fd)

```
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    sh 'echo "Testing Stage"'
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    sh 'echo "Package"'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'echo "Deploy"'
                }
            }
        }
        stage('Clean up') {
            steps {
                script {
                    sh 'echo "Clean up"'
                }
            }
        }
    }
}

```

![image](https://github.com/user-attachments/assets/1d25e93b-288d-4a79-90cf-e540cb4cb0b3)

![image](https://github.com/user-attachments/assets/06f24263-effd-417e-a071-c9525dbb4bc1)


## Running Ansible Playbook from Jenkins.

Now We have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:

1. Installing Ansible on Jenkins.

![image](https://github.com/user-attachments/assets/64c5c1b1-fc9f-4fbe-a9e8-a6a0d09a5790)

2. Installing Ansible plugin in Jenkins UI.
![image](https://github.com/user-attachments/assets/62764bfb-a61c-4322-970b-fd66988c3279)

Configuring the path on ansible on jenkins.. For that need to go manage Jenkins > Configure tools.

![image](https://github.com/user-attachments/assets/7904d1ab-bd73-422e-bb90-f7ad1717de98)


4. Creating Jenkinsfile from scratch. (Delete all you currently have in there and start all over to get Ansible to run successfully).

Deleted the all content of the jenkinsfile and Created the new one

![image](https://github.com/user-attachments/assets/dffbbe98-b2b4-4d8b-8c4e-a4dac2600dd0)


- Start writing it again. to do this, we can make use of pipeline syntax to ensure we get the exact command for what we intend to achieve. here is how the Jenkinsfile should look eventually .

```
pipeline {
    agent any

    environment {
        ANSIBLE_CONFIG = "${WORKSPACE}/deploy/ansible.cfg"
    }

    stages {
        stage('Initial cleanup') {
            steps {
                dir("${WORKSPACE}") {
                    deleteDir()
                }
            }
        }

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/mimi-netizen/ansible-config-mgt.git'
            }
        }

        stage('Prepare Ansible For Execution') {
            steps {
                sh 'echo ${WORKSPACE}'
                sh 'sed -i "3 a roles_path=${WORKSPACE}/roles" ${WORKSPACE}/deploy/ansible.cfg'
            }
        }

        stage('Test SSH Connections') {
            steps {
                script {
                    def allHosts = [
                        'ubuntu@172.31.8.154',
                        'ubuntu@172.31.3.198',
                        'ec2-user@172.31.6.165'
                    ]

                    sshagent(['private-key']) {
                        allHosts.each { host ->
                            sh "ssh -o StrictHostKeyChecking=no ${host} exit"
                        }
                    }
                }
            }
        }

        stage('Run Ansible playbook') {
            steps {
                sshagent(['private-key']) {
                    ansiblePlaybook(
                        become: true,
                        credentialsId: 'private-key',
                        disableHostKeyChecking: true,
                        installation: 'ansible',
                        inventory: '${WORKSPACE}/inventory/dev.yml',
                        playbook: '${WORKSPACE}/playbooks/site.yml'
                    )
                }
            }
        }

        stage('Clean Workspace after build') {
            steps {
                cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true)
            }
        }
    }
}

```

![image](https://github.com/user-attachments/assets/7d91e99d-5d94-4f3b-bb4c-cac338f46ee4)


# Note: Ensure that Ansible runs against the Dev environment successfully.

Possible errors to watch out for:

1. Ensure that the git module in Jenkinsfile is checking out SCM to main branch instead of master (GitHub has discontinued the use of Master due to Black Lives Matter. You can read more here)

2. Jenkins needs to export the ANSIBLE_CONFIG environment variable. You can put the .ansible.cfg file alongside Jenkinsfile in the deploy directory. This way, anyone can easily identify that everything in there relates to deployment. Then, using the Pipeline Syntax tool in Ansible, generate the syntax to create environment variables to set. https://wiki.jenkins.io/display/JENKINS/Building+a+software+project

Possible issues to watch out for when you implement this

1. Remember that ansible.cfg must be exported to environment variable so that Ansible knows where to find Roles. But because you will possibly run Jenkins from different git branches, the location of Ansible roles will change. Therefore, you must handle this dynamically. You can use Linux Stream Editor sed to update the section roles_path each time there is an execution. You may not have this issue if you run only from the main branch.

2. If you push new changes to Git so that Jenkins failure can be fixed. You might observe that your change may sometimes have no effect. Even though your change is the actual fix required. This can be because Jenkins did not download the latest code from GitHub. Ensure that you start the Jenkinsfile with a clean up step to always delete the previous workspace before running a new one. Sometimes you might need to login to the Jenkins Linux server to verify the files in the workspace to confirm that what you are actually expecting is there. Otherwise, you can spend hours trying to figure out why Jenkins is still failing, when you have pushed up possible changes to fix the error.

3. Another possible reason for Jenkins failure sometimes, is because you have indicated in the Jenkinsfile to check out the main git branch, and you are running a pipeline from another branch. So, always verify by logging onto the Jenkins box to check the workspace, and run git branch command to confirm that the branch you are expecting is there.




### To ensure jenkins properly connects to all servers, install another plugin called ssh agent.

![image](https://github.com/user-attachments/assets/c1161951-2964-4db5-a6f3-57be092c3606)


### Then go to manage jenkins > `credentials` > `global` > `add credentials`

Then follow the steps below:
- Kind: SSH Username with private key.
- Scope: Global (Jenkins, nodes, items, all child items, etc).
- ID: private-key (or any ID you prefer)
- Username: Leave it blank or set a default value (e.g., defaultuser) # This is because we are using servers of different username i.e ubbuntu and ec2-user. This value won’t be used because the actual usernames will be specified in the Ansible inventory file.
- Private Key: Enter the private key directly.

![image](https://github.com/user-attachments/assets/ab17bd04-9cee-4dcd-a313-28fcb94c2857)

#### Updating the site.yml file

Update inventory/dev.yml by specifying the private IP address of the servers

![image](https://github.com/user-attachments/assets/b8978a7c-8608-42d5-aa01-6b873a764e13)

Update the site.yml

![image](https://github.com/user-attachments/assets/75f1c1b6-c13a-402e-8696-c6f3754a83db)


After fixing all errors final result looks

![image](https://github.com/user-attachments/assets/e2e08aa7-5d09-4531-88e5-d9aa2b7dc5ab)


![image](https://github.com/user-attachments/assets/a40d0bfe-eef1-4e33-a73a-ac851963fc07)


![image](https://github.com/user-attachments/assets/be982f02-ed1d-46f1-b7bf-ec9ad8093d9e)


If everything goes well for you, it means, the Dev environment has an up-to-date configuration. But what if we need to deploy to other environments?

- Are we going to manually update the Jenkinsfile to point inventory to those environments? such as sit, uat, pentest, etc.
- Or do we need a dedicated git branch for each environment, and have the inventory part hard coded there.


Think about those for a minute and try to work out which one sounds more like a better solution.

Manually updating the Jenkinsfile is definitely not an option. And that should be obvious to you at this point. Because we try to automate things as much as possible.

What we will be doing is to parameterise the deployment. So that at the point of execution, the appropriate values are applied.


## Parameterizing Jenkinsfile For Ansible Deployment


To deploy to other environments, we will need to use parameters

1. Update sit inventory with new servers (inventory/sit.yml)

```
[tooling]
<SIT-Tooling-Web-Server-Private-IP-Address>

[todo]
<SIT-Todo-Web-Server-Private-IP-Address>

[nginx]
<SIT-Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<SIT-DB-Server-Private-IP-Address>

```

![image](https://github.com/user-attachments/assets/2bf82c36-ad64-42fa-a28e-f9736595da69)


2. Update Jenkinsfile to introduce parameterization. Below is just one parameter. It has a default value in case if no value is specified at execution. It also has a description so that everyone is aware of its purpose.


```
pipeline {
    agent any

    parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy
      configuration')
    }


```
![image](https://github.com/user-attachments/assets/05326e7a-86e9-4a2a-b5b7-3f9959cd31ea)

3. In the Ansible execution section, remove the hardcoded inventory/dev and replace with `${inventory} From now on, each time you hit on execute, it will expect an input.

![image](https://github.com/user-attachments/assets/4ebaf23b-7715-4d9c-b1df-b124c2b08e66)


4.  We can see we got build with parameters option on pipeline to execute So click on build with parameters button.
   
![image](https://github.com/user-attachments/assets/790d9660-5f56-473a-a129-07adea214422)

#### Notice that the default value loads up, but we can now specify which environment we want to deploy the configuration to. Simply type sit and hit Run

![image](https://github.com/user-attachments/assets/6590d11c-7d6d-492b-83dc-81ed1d7d9235)


Build got success for environemtn SIT ..

![image](https://github.com/user-attachments/assets/e68759b9-9343-480f-97df-f316ea36e016)


5. Add another parameter. This time, introduce tagging in Ansible. You can limit the Ansible execution to a specific role or playbook desired. Therefore, add an Ansible tag to run against webserver only. Test this locally first to get the experience. Once you understand this, update Jenkinsfile and run it from Jenkins.

- Update playbook/site.yml with tags

![image](https://github.com/user-attachments/assets/4bf1fb4a-ab0f-403a-bfbe-bd0e3cd400b8)

Add another parameter to the jenkinsfile. Name the parameter ansible_tags and the default value webserver

```  string(name: 'ansible_tags', defaultValue: 'webserver', description: 'Ansible tags to run specific roles or tasks') ```

![image](https://github.com/user-attachments/assets/b053eff7-b494-4cde-bf89-c8160f039c6f)


![image](https://github.com/user-attachments/assets/9fc2cbcd-353c-4575-b6cb-7613f8cdfa4b)

We can do build the pipeline through Blue Ocean..

![image](https://github.com/user-attachments/assets/edbaac17-497d-4e88-ac62-4c86fed99232)


![image](https://github.com/user-attachments/assets/acd74991-56d5-41fb-9bb2-84f692446a18)




## CI/CD Pipline for TODO Application:

We already have tooling website as a part of deployment through Ansible. Here we will introduce another PHP application to add to the list of software products we are managing in our infrastructure. The good thing with this particular application is that it has unit tests, and it is an ideal application to show an end-to-end CI/CD pipeline for a particular application.

Our goal here is to deploy the application onto servers directly from Artifactory rather than from git If you have not updated Ansible with an Artifactory role, simply use this guide to create an Ansible role for Artifactory (ignore the Nginx part). Configure Artifactory on Ubuntu 20.04 https://www.atlantic.net/dedicated-server-hosting/how-to-install-jfrog-artifactory-on-ubuntu-22-04/

```
/path/to/your/laravel/project
├── app
│   ├── Console
│   ├── Exceptions
│   ├── Http
│   │   ├── Controllers
│   │   ├── Middleware
│   ├── Models
│   ├── Providers
├── bootstrap
│   ├── cache
├── config
│   ├── app.php
│   ├── database.php
│   └── ...
├── database
│   ├── factories
│   ├── migrations
│   ├── seeders
├── public
│   ├── index.php
│   ├── css
│   ├── js
│   ├── ...
├── resources
│   ├── js
│   ├── lang
│   ├── views
│   └── ...
├── routes
│   ├── api.php
│   ├── channels.php
│   ├── console.php
│   ├── web.php
├── storage
│   ├── app
│   ├── framework
│   ├── logs
├── tests
│   ├── Feature
│   ├── Unit
├── vendor
├── .env
├── artisan
├── composer.json
├── composer.lock
├── package.json
├── phpunit.xml
└── webpack.mix.js

```

#### Prerequests  Make sure port 8082 is opened in artifactory server.

Adding the jfrog repo roles using following commond:

```
ansible-galaxy collection install jfrog.platform
```
![image](https://github.com/user-attachments/assets/1abf3f89-1941-4487-8e12-ee0d2c5e0577)

Update ```playbook/site.yml```

![image](https://github.com/user-attachments/assets/e4f4b969-25fa-475b-b570-c13dcd9fcae9)

![image](https://github.com/user-attachments/assets/deb4a876-1791-4cf4-a461-c8ea5c022dc3)

![image](https://github.com/user-attachments/assets/7e3beae6-e32a-4c76-9d5f-145b32b8d1cf)

![image](https://github.com/user-attachments/assets/d2fac930-2495-4f7b-9f50-4095eea86e6f)


Run the playbook against ci.yml to install jfrog artifactory

![image](https://github.com/user-attachments/assets/307daaa2-349a-4c5f-955b-1c9df038bf19)

![image](https://github.com/user-attachments/assets/c4837649-08e8-40a1-a1b1-d95bf8ee9f6a)


After build Success Also it doesnt Installed the jfrog Artifactory on Ubuntu Server We can try using jfrog Cloud https://my.jfrog.com/login/

![image](https://github.com/user-attachments/assets/5b1022ca-af7b-456b-857c-94489273d044)


Setting Up the Jenkins..

![image](https://github.com/user-attachments/assets/5fc31e33-8efc-4527-8d17-eeee401a6c7e)



## Phase 1 - Prepare Jenkins

1. Fork repository To Own Github Account:

```
https://github.com/StegTechHub/php-todo/fork
```

![image](https://github.com/user-attachments/assets/3631b941-1728-425a-91be-637ff4daacff)

2. On you Jenkins server, install PHP, its dependencies and Composer tool (Feel free to do this manually at first, then update your Ansible accordingly later)

```
sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
```
![image](https://github.com/user-attachments/assets/f0f56bee-ee9a-4bc8-a525-2c62c329ebad)

Install Composer Download the Installer:
```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
```

Install Composer Globally

```
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```
Remove the Installer

```
php -r "unlink('composer-setup.php');"
```

Verify Installation

```
php -v
```

```
composer -v
```

![image](https://github.com/user-attachments/assets/48656dc7-f253-46be-982e-a6fb44c06057)


3. Install Jenkins plugins.

    1. Plot plugin.
    2. Artifactory plugin.

* We will use plot plugin to display tests reports, and code coverage information.
* The Artifactory plugin will be used to easily upload code artifacts into an Artifactory server.

  
```
pipeline {
    agent any

  stages {

    stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/ksal1235/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }

}
```

![image](https://github.com/user-attachments/assets/ff60a7b5-2092-4711-855a-77da0bd33b13)

2. Using Blue Ocean, create a multibranch Jenkins pipeline

![image](https://github.com/user-attachments/assets/733d30d8-b0fb-4136-8dfb-0eb156eeab67)


3. In jenkins server Install mysql client:

```
sudo apt install mysql-client -y
```

-  On the database server, create database and user.

```
sudo apt install mysql-client -y
```

-  On your database server, create the necessary database and user as specified in the TODO application `.env.`

```
Create database homestead;
CREATE USER 'homestead'@'%' IDENTIFIED BY 'sePret^i';
GRANT ALL PRIVILEGES ON * . * TO 'homestead'@'%';

```
![image](https://github.com/user-attachments/assets/67e10ba3-2876-4036-b0a7-630991df4d1b)


Login into the DB-server(mysql server) and set the the bind address to 0.0.0.0:

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

![image](https://github.com/user-attachments/assets/05422662-1d12-4306-b566-926201233ed7)


Restart the mysql- server:

```
sudo systemctl restart mysql
```

4. Create a `.env` file in TODO repo and update it with the credentials to connect the database, use sample the code below :

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=SomeRandomString
APP_URL=http://localhost

DB_HOST=172.31.8.236
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=sePret^i

CACHE_DRIVER=file
SESSION_DRIVER=file
QUEUE_DRIVER=sync

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_DRIVER=smtp
MAIL_HOST=mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

![image](https://github.com/user-attachments/assets/cc4b4ed2-76e6-4f1a-8c12-2d09f55e6e12)

5. Update Jenkinsfile with proper pipeline configuration.

```

pipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }

    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/ksal1235/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}

```

`` Notice the Prepare Dependencies section.

- Composer is used by PHP to install all the dependent libraries used by the application
- php artisan uses the .env file to setup the required database objects


Note : The php version installed by the composer is 8.3.6 while the php version of the todo application is 7.4. Ensure to remove the current version and install php 7.4 and its dependencies to avoid error.


5. Run the TODO Pipeline:

![image](https://github.com/user-attachments/assets/7a8447fd-4f9c-4da2-9513-02eca45c715c)

– After successful run of this step, login to the database,password:root

```
mysql -u root -p
```
![image](https://github.com/user-attachments/assets/826e08ed-c997-446f-9db5-96c84e32c14b)

1. Update the Jenkinsfile to include Unit tests step

first install

```
composer require --dev phpunit/phpunit
```
then update Jenkinsfile

``
stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      }
    }
``

Phase 3 – Code Quality Analysis

This is one of the areas where developers, architects and many stakeholders are mostly interested in as far as product development is concerned. As a DevOps engineer, you also have a role to play. Especially when it comes to setting up the tools.

The data produced by phploc can be ploted onto graphs in Jenkins.

- First, install phploc and phpunit on Jenkins Server.

```
# Install phploc
wget -O phploc.phar https://phar.phpunit.de/phploc-6.0.0.phar

# Add the permission to make file executeable
chmod +x phploc.phar

# Reolad Shell Config
sudo mv phploc.phar /usr/local/bin/phploc

# verify the installation
phploc --version

# Download the phpunit PHAR file
wget -O phpunit.phar https://phar.phpunit.de/phpunit-9.5.10.phar

# Make the PHAR file executable
chmod +x phpunit.phar

# Move the PHAR file to a directory in your PATH
sudo mv phpunit.phar /usr/local/bin/phpunit

#Verify the installation
phpunit --version

```
1. Add the code analysis step in Jenkinsfile. The output of the data will be saved in ```build/logs/phploc.csv file.```

```
stage('Code Analysis') {
  steps {
        sh 'phploc app/ --log-csv build/logs/phploc.csv'

  }
}

```

2. Plot the data using plot Jenkins plugin.

This plugin provides generic plotting (or graphing) capabilities in Jenkins. It will plot one or more single values variations across builds in one or more plots. Plots for a particular job (or project) are configured in the job configuration screen, where each field has additional help information. Each plot can have one or more lines (called data series). After each build completes the plots’ data series latest values are pulled from the CSV file generated by phploc.

```
stage('Plot Code Coverage Report') {
            steps {
                plot csvFileName: 'plot-loc.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of Code', yaxis: 'Lines of Code'
                plot csvFileName: 'plot-structures.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
                plot csvFileName: 'plot-avg-length.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
                plot csvFileName: 'plot-complexity.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'
                plot csvFileName: 'plot-classes.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
                plot csvFileName: 'plot-methods.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
                plot csvFileName: 'plot-constants.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
                plot csvFileName: 'plot-testing.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
                plot csvFileName: 'plot-lloc.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
                plot csvFileName: 'plot-functions.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
                plot csvFileName: 'plot-objects.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'
            }
        }
```

You should now see a Plot menu item on the left menu. Click on it to see the charts. (The analytics may not mean much to you as it is meant to be read by developers. So, you need not worry much about it – this is just to give you an idea of the real-world implementation).


3. Bundle the application code for into an artifact (archived package) upload to Artifactory

```

stage ('Package Artifact') {
    steps {
            sh 'zip -qr php-todo.zip ${WORKSPACE}/*'
     }
    }
```

4. Publish the resulted artifact into Artifactory


```
stage ('Upload Artifact to Artifactory') {
        steps {
            script {
                def server = Artifactory.server 'artifactory-server'
                def uploadSpec = """{
                    "files": [
                    {
                    "pattern": "php-todo.zip",
                    "target": "<name-of-artifact-repository>/php-todo",
                    "props": "type=zip;status=ready"

                    }
                    ]
                }"""
                println "Upload Spec: ${uploadSpec}"
                                  try {
                                      server.upload spec: uploadSpec
                                      println "Upload successful"
                                  } catch (Exception e) {
                                      println "Upload failed: ${e.message}"
                                    }
            }
          }
}

```


5. Deploy the application to the dev environment by launching Ansible pipeline

```
stage ('Deploy to Dev Environment') {
    steps {
    build job: 'ansible-project/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
    }
  }
```

The build job used in this step tells Jenkins to start another job. In this case it is the ansible-project job, and we are targeting the main branch. Hence, we have ansible-project/main. Since the Ansible project requires parameters to be passed in, we have included this by specifying the parameters section. The name of the parameter is env and its value is dev. Meaning, deploy to the Development environment.

Make sure to update artifactory login details in the todo deployment configuration file in ansible-config-mgt project

- Create a task to set up the Dev environment and deploy artifact to webserver (todo server)

```
# # tasks file for webserver - php-todo
---
# Install Apache
- name: Install Apache
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name: httpd
    state: present

# Install EPEL release
- name: Install EPEL release
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name: https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
    state: present

# Install dnf-utils and Remi repository
- name: Install dnf-utils and Remi repository
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name:
      - dnf-utils
      - http://rpms.remirepo.net/enterprise/remi-release-9.rpm
    state: present

# Reset PHP module
- name: Reset PHP module
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: dnf module reset php -y

# Enable PHP 7.4 module
- name: Enable PHP 7.4 module
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: dnf module enable php:remi-7.4 -y

# Install PHP and extensions
- name: Install PHP and extensions
  remote_user: ec2-user
  become: true
  ansible.builtin.yum:
    name:
      - php
      - php-opcache
      - php-gd
      - php-curl
      - php-mysqlnd
      - php-common
      - php-mbstring
      - php-intl
      - php-xml
      - php-fpm
      - php-json
    state: present
    enablerepo: remi-7.4

# Ensure PHP-FPM service is started and enabled
- name: Ensure PHP-FPM service is started and enabled
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: php-fpm
    state: started
    enabled: true

# Set SELinux boolean for httpd_execmem
- name: Set SELinux boolean for httpd_execmem
  remote_user: ec2-user
  become: true
  ansible.builtin.seboolean:
    name: httpd_execmem
    state: true
    persistent: yes

# Ensure httpd service is started and enabled
- name: Ensure httpd service is started and enabled
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: httpd
    state: started
    enabled: true

# Download the artifact
- name: Download the artifact
  remote_user: ec2-user
  become: true
  ansible.builtin.get_url:
    url: http://52.52.188.173:8082/artifactory/todo-dev-local/php-todo.zip
    dest: /home/ec2-user/php-todo.zip
    url_username: admin
    url_password: <password>

- name: Ensure unzip is installed
  become: yes
  ansible.builtin.yum:
    name: unzip
    state: present
  when: ansible_os_family == "RedHat"

- name: Unzip the artifacts
  ansible.builtin.unarchive:
    src: /home/ec2-user/php-todo.zip
    dest: /var/www/html/
    remote_src: yes

# Unzip the artifacts
- name: Unzip the artifacts
  ansible.builtin.unarchive:
    src: /home/ec2-user/php-todo.zip
    dest: /home/ec2-user/
    remote_src: yes
  become: true

# Run chown on /var/www/html
- name: Run chown on /var/www/html
  ansible.builtin.command:
    cmd: chown -R $USER:$USER /var/www/html
  become: true

# Deploy the code
- name: Deploy the code
  remote_user: ec2-user
  become: true
  ansible.builtin.copy:
    src: /home/ec2-user/var/lib/jenkins/workspace/php-todo-pipeline/.
    dest: /var/www/html/
    force: yes
    remote_src: yes

# Delete the zip file after unzipping
- name: Delete the zip file after unzipping
  ansible.builtin.file:
    path: /home/ec2-user/php-todo.zip
    state: absent
  become: true

# Delete the unzipped files after copying
- name: Delete the unzipped files after copying
  ansible.builtin.file:
    path: /home/ec2-user/php-todo/
    state: absent
  become: true

# Remove Apache default welcome page on RedHat-based systems
- name: Remove Apache default welcome page
  remote_user: ec2-user
  become: true
  ansible.builtin.file:
    path: /etc/httpd/conf.d/welcome.conf
    state: absent
  when: ansible_os_family == "RedHat"

# Restart httpd
- name: Restart httpd
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: httpd
    state: restarted

# Verify PHP version
- name: Verify PHP version
  remote_user: ec2-user
  become: true
  ansible.builtin.command: php -v
  register: php_version

# Display PHP version
- name: Display PHP version
  remote_user: ec2-user
  become: true
  ansible.builtin.debug:
    msg: "{{ php_version.stdout }}"

# Set SELinux context for web directory and PHP files
- name: Set SELinux context for web directory
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chcon -R -t httpd_sys_content_t /var/www/html

- name: Set SELinux context for PHP writable files
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chcon -R -t httpd_sys_rw_content_t /var/www/html

# Generate a new APP_KEY if missing or invalid
- name: Generate a new APP_KEY if missing or invalid
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: php artisan key:generate
  args:
    chdir: /var/www/html

# Clear the configuration cache
- name: Clear the configuration cache
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: php artisan config:cache
  args:
    chdir: /var/www/html

# Ensure .env file and config directory have correct permissions
- name: Ensure .env file and config directory have correct permissions - chown
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chown -R apache:apache /var/www/html/

- name: Ensure .env file and config directory have correct permissions - chmod
  remote_user: ec2-user
  become: true
  ansible.builtin.command:
    cmd: chmod -R 775 /var/www/html/

# Restart the web server and PHP-FPM
- name: Restart the web server
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: httpd
    state: restarted

- name: Restart PHP-FPM
  remote_user: ec2-user
  become: true
  ansible.builtin.service:
    name: php-fpm
    state: restarted
```

But how are we certain that the code being deployed has the quality that meets corporate and customer requirements? Even though we have implemented Unit Tests and Code Coverage Analysis with phpunit and phploc, we still need to implement Quality Gate to ensure that ONLY code with the required code coverage, and other quality standards make it through to the environments.

To achieve this, we need to configure SonarQube – An open-source platform developed by SonarSource for continuous inspection of code quality to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.


** SONARQUBE INSTALLATION

Before we start getting hands on with SonarQube configuration, it is incredibly important to understand a few concepts:

Software Quality – The degree to which a software component, system or process meets specified requirements based on user needs and expectations.
Software Quality Gates – Quality gates are basically acceptance criteria which are usually presented as a set of predefined quality criteria that a software development project must meet in order to proceed from one stage of its lifecycle to the next one.
SonarQube is a tool that can be used to create quality gates for software projects, and the ultimate goal is to be able to ship only quality software code.

Despite that DevOps CI/CD pipeline helps with fast software delivery, it is of the same importance to ensure the quality of such delivery. Hence, we will need SonarQube to set up Quality gates. In this project we will use predefined Quality Gates (also known as The Sonar Way ). Software testers and developers would normally work with project leads and architects to create custom quality gates.

Install SonarQube on Ubuntu 24.04 With PostgreSQL as Backend Database
Here is a manual approach to installation. Ensure that you can to automate the same with Ansible.

Below is a step by step guide how to install SonarQube 7.9.3 version. It has a strong prerequisite to have Java installed since the tool is Java-based. MySQL support for SonarQube is deprecated, therefore we will be using PostgreSQL.

We will make some Linux Kernel configuration changes to ensure optimal performance of the tool – we will increase vm.max_map_count, file discriptor and ulimit.

**** Tune Linux Kernel

This can be achieved by making session changes which does not persist beyond the current session terminal.
  
```
sudo sysctl -w vm.max_map_count=262144
sudo sysctl -w fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```


To make a permanent change, edit the file /etc/security/limits.conf and append the below

```
vi /etc/security/limits.conf

```

```
sonarqube   -   nofile   65536
sonarqube   -   nproc    4096
```

Before installing, let us update and upgrade system packages:

```
sudo apt-get update
sudo apt-get upgrade
```

Install wget and unzip packages
```
sudo apt-get install wget unzip -y
```
Install OpenJDK and Java Runtime Environment (JRE) 11

```
sudo apt-get install openjdk-11-jdk -y
sudo apt-get install openjdk-11-jre -y
```

Set default JDK – To set default JDK or switch to OpenJDK enter below command:

```
sudo update-alternatives --config java
```
If you have multiple versions of Java installed, you should see a list like below:

```
Selection    Path                                            Priority   Status

------------------------------------------------------------

  0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode

  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode
```

Type "1" to switch OpenJDK 11

Verify the set JAVA Version:
```
java -version
```

** Install and Setup PostgreSQL 10 Database for SonarQube.

The command below will add PostgreSQL repo to the repo list:

```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

Download PostgreSQL software.
```
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```

Install PostgreSQL Database Server.
```
sudo apt-get -y install postgresql postgresql-contrib
```

Start PostgreSQL Database Server

```
sudo systemctl start postgresql
```

Enable it to start automatically at boot time
```
sudo systemctl enable postgresql
```

Change the password for default postgres user (Pass in the password you intend to use, and remember to save it somewhere)

```
sudo passwd postgres
```

Switch to the postgres user

```
su  postgre -
```

Create a new user by typing

```
createuser sonar
```

Switch to the PostgreSQL shell

```
psql
```

Set a password for the newly created user for SonarQube database

```
ALTER USER sonar WITH ENCRYPTED password 'sonar';

```


Create a new database for PostgreSQL database by running:
```
CREATE DATABASE sonarqube OWNER sonar;
```

Grant all privileges to sonar user on sonarqube Database.
```
grant all privileges on DATABASE sonarqube to sonar;
```

Exit from the psql shell:

```
\q

```

** Install SonarQube on Ubuntu 24.04 LTS.

Navigate to the tmp directory to temporarily download the installation files

```
cd /tmp && sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.9.3.zip
```

Unzip the archive setup to /opt directory
```
sudo unzip sonarqube-7.9.3.zip -d /opt
```

Move extracted setup to /opt/sonarqube directory

```
sudo mv /opt/sonarqube-7.9.3 /opt/sonarqube
```

** CONFIGURE SONARQUBE

We cannot run SonarQube as a root user, if you run using root user it will stop automatically. The ideal approach will be to create a separate group and a user to run SonarQube

Create a group sonar

```
    sudo groupadd sonar
```

Now add a user with control over the /opt/sonarqube directory

```
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube -R
```

Open SonarQube configuration file using your favourite text editor (e.g., nano or vim)

```
sudo vim /opt/sonarqube/conf/sonar.properties
```

Find the following lines:
```
#sonar.jdbc.username=
#sonar.jdbc.password=
```

Uncomment them and provide the values of PostgreSQL Database username and password:

```

# DATABASE

# IMPORTANT:

# - The embedded H2 database is used by default. It is recommended for tests but not for

#   production use. Supported databases are Oracle, PostgreSQL and Microsoft SQLServer.

# - Changes to database connection URL (sonar.jdbc.url) can affect SonarSource licensed products.

# User credentials.

# Permissions to create tables, indices and triggers must be granted to JDBC user.

# The schema must be created first.

sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
```
![image](https://github.com/user-attachments/assets/76503c8b-142f-463d-a325-d0be70379a9d)

#### Edit the sonar script file and set RUN_AS_USER

````
sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh
````

```
# If specified, the Wrapper will be run as the specified user.

# IMPORTANT - Make sure that the user has the required privileges to write

#  the PID file and wrapper.log files.  Failure to be able to write the log

#  file will cause the Wrapper to exit without any way to write out an error

#  message.

# NOTE - This will set the user which is used to run the Wrapper as well as

#  the JVM and is not useful in situations where a privileged resource or

#  port needs to be allocated prior to the user being changed.

RUN_AS_USER=sonar
```

![image](https://github.com/user-attachments/assets/90b1acc2-906b-4c62-9e55-185cecf9a231)

#### Now, to start SonarQube we need to do following: Switch to sonar user

```
sudo su sonar
```

Move to the script directory
```
cd /opt/sonarqube/bin/linux-x86-64/
```

#### Run the script to start SonarQube
```
./sonar.sh start
```

#### Expected output shall be as:

````
Starting SonarQube...

Started SonarQube
````

#### Check SonarQube running status:

````
./sonar.sh status
````

#### Sample Output below:
````
./sonar.sh status

SonarQube is running (176483).
````

#### To check SonarQube logs, navigate to /opt/sonarqube/logs/sonar.log directory.

````
tail /opt/sonarqube/logs/sonar.log
````
![image](https://github.com/user-attachments/assets/7c6ce653-695b-4cc6-a365-03740d80589a)


## Output

You can see that SonarQube is up and running

Configure SonarQube to run as a systemd service

Stop the currently running SonarQube service

```
cd /opt/sonarqube/bin/linux-x86-64/
```

#### Run the script to start SonarQube.

```
./sonar.sh stop
```

#### Create a systemd service file for SonarQube to run as System Startup.
```
 sudo nano /etc/systemd/system/sonar.service
```

Add the configuration below for systemd to determine how to start, stop, check status, or restart the SonarQube service.

```
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target

```

#### Save the file and control the service with systemctl.

```
sudo systemctl start sonar
sudo systemctl enable sonar
sudo systemctl status sonar
```


![image](https://github.com/user-attachments/assets/b6903b2b-2c7a-47f7-bb28-ff99a2c8c6f4)

####  Access SonarQube
To access SonarQube using browser, type server's IP address followed by port 9000

```
http://server_IP:9000 OR http://localhost:9000

```
Login to SonarQube with default administrator username - admin and password - admin


![image](https://github.com/user-attachments/assets/351e1d49-e60c-4095-a239-0ebee02f4b47)


![image](https://github.com/user-attachments/assets/63150e5a-ab74-4ffa-800e-febc66f0900c)


![image](https://github.com/user-attachments/assets/f3167812-94ca-422f-a8bd-684c24938895)


# Configure SonarQube and Jenkins For Quality Gate

- In Jenkins, install SonarScanner plugin


![image](https://github.com/user-attachments/assets/0133b914-bf30-4860-8c9a-995ccd9d5973)

Navigate to configure system in Jenkins. Add SonarQube server as shown below: Manage Jenkins > Configure System

![image](https://github.com/user-attachments/assets/12b68c09-368e-4f22-9d55-85d3e4999812)

- Generate authentication token in SonarQube

User > My Account > Security > Generate Tokens

![image](https://github.com/user-attachments/assets/aa207bec-966d-4b88-8a06-27968debb16c)

- Configure Quality Gate Jenkins Webhook in SonarQube – The URL should point to your Jenkins server http://{JENKINS_HOST}/sonarqube-webhook/

Administration > Configuration > Webhooks > Create

![image](https://github.com/user-attachments/assets/b3132bf1-cab8-415c-817e-eac467b9cd29)


![image](https://github.com/user-attachments/assets/56f2df49-d983-4a22-bf40-7e778bf37bfe)

- Setup SonarQube scanner from Jenkins – Global Tool Configuration

Manage Jenkins > Global Tool Configuration.

![image](https://github.com/user-attachments/assets/47297a4f-8c0e-4a69-9278-72ce6fb4d26a)

## Update Jenkins Pipeline to include SonarQube scanning and Quality Gate

Below is the snippet for a Quality Gate stage in Jenkinsfile

  stage('SonarQube Quality Gate') {
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner"
            }

        }
    }


``NOTE: The above step will fail because we have not updated sonar-scanner.properties``


![image](https://github.com/user-attachments/assets/8b344279-9862-4eb0-aa55-d32b46f16247)

- Configure sonar-scanner.properties – From the step above, Jenkins will install the scanner tool on the Linux server. You will need to go into the tools directory on the server to configure the properties file in which SonarQube will require to function during pipeline execution.

``cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/conf/``


![image](https://github.com/user-attachments/assets/210c8362-9bb1-4d39-8823-bf7068ded37d)

Open sonar-scanner.properties file.

```
sudo vi sonar-scanner.properties
```

Add configuration related to php-todo project.

```
sonar.host.url=http://<SonarQube-Server-IP-address>:9000
sonar.projectKey=php-todo
#----- Default source code encoding
sonar.sourceEncoding=UTF-8
sonar.php.exclusions=**/vendor/**
sonar.php.coverage.reportPaths=build/logs/clover.xml
sonar.php.tests.reportPath=build/logs/junit.xml
```

![image](https://github.com/user-attachments/assets/d4ac3a36-f450-4608-bb54-ac7b8c4f952c)

HINT: To know what exactly to put inside the sonar-scanner.properties file, SonarQube has a configurations page where you can get some directions.


![image](https://github.com/user-attachments/assets/603c7124-7d2c-40dc-b9b5-a37e91112510)


A brief explanation of what is going on the the stage – set the environment variable for the scannerHome use the same name used when you configured SonarQube Scanner from Jenkins Global Tool Configuration. If you remember, the name was SonarQubeScanner. Then, within the steps use shell to run the scanner from bin directory.

To further examine the configuration of the scanner tool on the Jenkins server – navigate into the tools directory

```
cd /var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQubeScanner/bin
```

List the content to see the scanner tool sonar-scanner. That is what we are calling in the pipeline script.

Output of (ls -latr)

![image](https://github.com/user-attachments/assets/98300686-628c-4c7f-b8e8-25ebf2ff1476)

So far you have been given code snippets on each of the stages within the Jenkinsfile. But, you should also be able to generate Jenkins configuration code yourself.

- To generate Jenkins code, navigate to the dashboard for the php-todo pipeline and click on the Pipeline Syntax menu item

**Dashboard** > **php-todo** > Pipeline Syntax

![image](https://github.com/user-attachments/assets/af04e128-6359-412c-9eb6-9fcad1341a21)

- Click on Steps and select withSonarQubeEnv – This appears in the list because of the previous SonarQube configurations you have done in Jenkins. Otherwise, it would not be there.

![image](https://github.com/user-attachments/assets/48f3c3ca-eb5b-4fb9-8f90-bdbb99c5bd5c)

Within the generated block, you will use the sh command to run shell on the server. For more advanced usage in other projects, you can add to bookmarks this SonarQube documentation page in your browser.

End-to-End Pipeline Overview

Indeed, this has been one of the longest projects from Project 1, and if everything has worked out for you so far, you should have a view like below:


![image](https://github.com/user-attachments/assets/23919632-c229-4280-9357-1fad48cc4a37)


![image](https://github.com/user-attachments/assets/1579b81f-14ca-47d8-804b-7879812bbbbe)


![image](https://github.com/user-attachments/assets/6dd8c007-bda9-4869-8c04-b41f6cb7aafe)

![image](https://github.com/user-attachments/assets/6e385303-5080-488f-9bbc-6866adfc3d22)

#### But we are not completely done yet!

The quality gate we just included has no effect. Why? Well, because if you go to the SonarQube UI, you will realise that we just pushed a poor-quality code onto the development environment.

- Navigate to php-todo project in SonarQube


![image](https://github.com/user-attachments/assets/39f34bb8-75aa-471b-9a9f-50aa38b26c07)

There are bugs, and there is 0.0% code coverage. (code coverage is a percentage of unit tests added by developers to test functions and objects in the code.

- If you click on php-todo project for further analysis, you will see that there is 6 hours’ worth of technical debt, code smells and security issues in the code.


  ![image](https://github.com/user-attachments/assets/a293f924-6160-4054-b812-a55e125550a6)

  In the development environment, this is acceptable as developers will need to keep iterating over their code towards perfection.

But as a DevOps engineer working on the pipeline, we must ensure that the quality gate step causes the pipeline to fail if the conditions for quality are not met.

Conditionally deploy to higher environments
In the real world, developers will work on feature branch in a repository (e.g., GitHub or GitLab). There are other branches that will be used differently to control how software releases are done. You will see such branches as:

1. Develop
2. Master or Main (The * is a place holder for a version number, Jira Ticket name or some description. It can be something like Release-1.0.0)
3. Feature/*
4. Release/*
5. Hotfix/* etc.

There is a very wide discussion around release strategy, and git branching strategies which in recent years are considered under what is known as GitFlow (Have a read and keep as a bookmark – it is a possible candidate for an interview discussion, so take it seriously!)

Assuming a basic gitflow implementation restricts only the develop branch to deploy code to Integration environment like sit.

Let us update our Jenkinsfile to implement this:

- First, we will include a When condition to run Quality Gate whenever the running branch is either develop, hotfix, release, main, or master.

```
when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
```

Then we add a timeout step to wait for SonarQube to complete analysis and successfully finish the pipeline only when code quality is acceptable.
```
 timeout(time: 1, unit: 'MINUTES') {
        waitForQualityGate abortPipeline: true
    }
```

The complete stage will now look like this:

```
 stage('SonarQube Quality Gate') {
      when { branch pattern: "^develop*|^hotfix*|^release*|^main*", comparator: "REGEXP"}
        environment {
            scannerHome = tool 'SonarQubeScanner'
        }
        steps {
            withSonarQubeEnv('sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dproject.settings=sonar-project.properties"
            }
            timeout(time: 1, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
            }
        }
    }

```
To test, create different branches and push to GitHub. You will realise that only branches other than develop, hotfix, release, main, or master will be able to deploy the code.


Create a new branch sonar and commit, push the new code.
![image](https://github.com/user-attachments/assets/1d7099ca-ab8c-47f5-b52f-2260bb1b39d6)


![image](https://github.com/user-attachments/assets/444c7993-eebe-4d88-b16f-a26f9f4ccc35)


![image](https://github.com/user-attachments/assets/1dfe335c-5a59-4ca7-89ce-a181bfb6295a)

```Notice that with the current state of the code, it cannot be deployed to Integration environments due to its quality. In the real world, DevOps engineers will push this back to developers to work on the code further, based on SonarQube quality report. Once everything is good with code quality, the pipeline will pass and proceed with sipping the codes further to a higher environment.```

### Complete the following tasks to finish Project 14

1. Introduce Jenkins agents/slaves – Add 2 more servers to be used as Jenkins slave. Configure Jenkins to run its pipeline jobs randomly on any available slave nodes. Let's add 2 more servers to be used as Jenkins slave and install java in them.

![image](https://github.com/user-attachments/assets/5c94bfdb-600a-4374-a52b-cb1d611b30dc)

```
# Install  java on slave nodes
sudo yum install java-11-openjdk-devel -y

# Check the java version
java --version

# Update packages
sudo apt update

# Install ansible on slave nodes
sudo apt install ansible -y
```

![image](https://github.com/user-attachments/assets/7995d424-e253-420d-a961-6428aaa44395)


![image](https://github.com/user-attachments/assets/17e1b553-8f1c-4f54-8b35-3f368f0b0f9d)


2. Configure webhook between Jenkins and GitHub to automatically run the pipeline when there is a code push. Let's Configure the new nodes on Jenkins Server.

Navigate to Dashboard > Manage Jenkins > Nodes, click on New node and enter a Name and click on create.


![image](https://github.com/user-attachments/assets/0b724f5d-b094-4691-959e-49bebc207ddc)

To connect to slave_one completed this fields and save.

- Name: slave_one
- Remote root directory: /opt/build (This can be any directory for the builds)
- Labels: slave_one
- save

To connect to slave_one, click on the slave_one and if you finish configuration save it you see

![image](https://github.com/user-attachments/assets/1dd9aa5f-9522-41f2-92ba-2cb48b0303b0)

![image](https://github.com/user-attachments/assets/fb78ca45-42fa-4bf7-a5d9-138b27ce7cda)


Use either options. In this case, I use the first option

Ensure to open port 5000 on the slave node server

Go to dashboard > manage jenkins > security > Agents, on Jenkins Set the TCP port for inbound agents to fixed and set the port at 5000

![image](https://github.com/user-attachments/assets/fe39a0ba-5c27-443e-8041-96bedcebfdae)

before running please check your public ip address of your jenkins is same as the if not go to Dashboard> manage Jenkins > system and update current IP

```
sudo mkdir -p /opt/build
sudo chown -R ubuntu:ubuntu /opt/build
sudo chmod -R 755 /opt/build
ls -ld /opt/build

# Download agent.jar to /opt/build. Make sure it has Jenkins IP here
curl -sO http://13.56.215.245:8080/jnlpJars/agent.jar

# Download agent.jar to /opt/build. Ensure it has Jenkins IP here
sudo java -jar agent.jar -url http://13.56.215.245:8080/ -secret 4dd3b0b23c99e63ac95d8d569f307c0f1f00d37e4ad381ffe2c21e18f17ae17f -name "slave_1" -workDir "/opt/build "
```



![image](https://github.com/user-attachments/assets/6c9825f9-e7f6-47a2-98ff-3e10c3a0ba95)
Verify that slave_1 is connected in jenkins


![image](https://github.com/user-attachments/assets/e53ee6fe-9825-4662-b6e6-d50ef66148f6)
Repeat same thing for the second

- Deploy the application to all the environments in order to deploy to all environment we Add these stages to our existing Jenkins pipeline script.

Development

``
stage ('Deploy to Dev Environment') {
            agent { label 'slave_one' } // Specify the Jenkins slave to use for deployment
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
            }
        }
``

Test Environment

```
        stage ('Deploy to Test Environment') {
            agent { label 'slave_two' } // Specify another Jenkins slave for deployment
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'pentest']], propagate: false, wait: true
            }
        }
```
```
        stage ('Deploy to Production Environment') {
            agent any
            steps {
                build job: 'ansible-config-mgt/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'ci']], propagate: false, wait: true
            }
        }

```


Optional – Experience pentesting in pentest environment by configuring Wireshark there and just explore for information sake only.Watch Wireshark Tutorial here
Ansible Role for Wireshark:

https://github.com/ymajik/ansible-role-wireshark (Ubuntu)

https://github.com/wtanaka/ansible-role-wireshark (RedHat)

The End of Project 14
We have just experienced one of the most interesting and complex projects in our Project Based Learning journey so far.




