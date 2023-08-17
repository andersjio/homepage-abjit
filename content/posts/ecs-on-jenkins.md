---
title: "ECS on Jenkins"
date: 2023-08-17T09:44:39+02:00
description: 'Using ECS labels in Jenkinsfiles'
image: images/ecs-jenkins-logo.png
draft: false
---
# Motivation
Researching different ways to improve stability and performance for the builds running on Jenkins , we decided to give AWS Elastic Container Service (ECS) a try.

This would enable the (re)use of Docker containers, but running on dedicated AWS hardware instead of some of on-premise infrastructure.

The setup of ECS in Jenkins was pretty straightforward following this [guide](https://aws.amazon.com/blogs/devops/set-up-a-build-pipeline-with-jenkins-and-amazon-ecs/) and using this Jenkins [plugin](https://plugins.jenkins.io/amazon-ecs/).

We quickly realized that adding ECS labels inside Jenkins would quickly grow to something unmanageable and at the same it had to be done by hand, because Jenkins Configuration as Code wasn't used.
The configuration page for Jenkins looked like this but required a lot of scrolling getting to the proper label and place

![ecs-jenkins-settings](/images/ecs-jenkins-settings.png)

# Problem
To avoid the cluttered Jenkins configuration page we wanted to be able to configure CPU, Memory and Docker image inside our Jenkinsfile directly in the pipelines.
This would allow to create a few ECS labels inside of the ECS configuration page in Jenkins and then re-use those labels in mutiple Jenkinsfiles but each with a custom Docker image.

Luckily that options is available in the plugin, detailed in the plugin section [here](https://plugins.jenkins.io/amazon-ecs/#plugin-content-usage)
![Jenkins ECS plugin settings](/images/ecs-plugin-settings.png)

Out-of-box this isn't supported and have to be enabled inside the ECS Jenkins configuration page as detailed [here](https://plugins.jenkins.io/amazon-ecs/#plugin-content-declarative-pipeline):

*If you want to override the label, ensure that you are not going to conflict with other labels configured elsewhere. Templates for dynamic agents exist until the agent dies, meaning other jobs requesting the same label (including dynamic agents on other runs of the same job!) run the chance of provisioning the dynamic agent's ECSTask.*

*Note: You have to configure list of settings to be allowed in the declarative pipeline first (see the Allowed Overrides setting). They are disabled by default for security reasons, to avoid non-privileged users to suddenly be able to change certain settings.*

Which caused this error in Jenkins:
![Jenkins ECS failure](/images/ecs-jenkins-failure.png)

But I couldn't find where to actually apply these settings. According to (this)[https://github.com/jenkinsci/amazon-ecs-plugin/issues/128] Github issue the labels that needs to be overridden should be added explicitly.

# Solution
After spending some time trying to find a description or image on how to add these labels, I found it after clicking around in the Jenkins UI.

* Go to: https://jenkins.example.com/manage/configureClouds/
* Click *Show More* on the "Amazon EC2 Container Service Cloud" label that you want to enable this for
* Click on *Advanced*
* Add the desired labels in the field called *Allowed declarative settings*

![Enable Jenkins labels in ECS](/images/ecs-enable-jenkins-labels.png)

That allowed this pipeline to work

![Jenkins ECS pipeline](/images/ecs-jenkins-pipeline.png)

Hopefully, this saves you the time I spend figuring this out!