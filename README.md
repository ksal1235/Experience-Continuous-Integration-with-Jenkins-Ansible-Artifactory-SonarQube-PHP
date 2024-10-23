![image](https://github.com/user-attachments/assets/69e3e905-29ce-4cc0-a8a8-6cd6e99f0976)![image](https://github.com/user-attachments/assets/b2005978-7f49-4c0d-86ec-e437434b62da)# Experience-Continuous-Integration-with-Jenkins-Ansible-Artifactory-SonarQube-PHP


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





