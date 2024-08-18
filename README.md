# Tooling Website Deployment with Continuous Integration: Introduction to Jenkins

Continuous Integration (CI) is a vital practice in modern software development, aimed at accelerating the development process while maintaining high code quality. In this project, we will explore how to streamline and automate routine tasks using Jenkins, a leading open-source automation server. Jenkins, initially known as "Hudson" and created by Kohsuke Kawaguchi, is renowned for its robustness in implementing CI/CD pipelines.

Our focus will be on leveraging Jenkins to enhance the deployment process of our website. Specifically, we will configure Jenkins to automatically update our Tooling Website with any changes made to the source code stored in our GitHub repository (https://github.com/OlavicDev/tooling.git).

## Task Overview

To achieve this, we will build upon the architecture established in Project 8 by integrating a Jenkins server. The steps include:

1. **Setting Up Jenkins**: Install and configure Jenkins on your server.
2. **Creating a Jenkins Job**: Set up a Jenkins job to monitor changes in the GitHub repository.
3. **Automating Deployment**: Configure the job to automatically deploy updated source code to an NFS server.

By the end of this project, your architecture will feature a Jenkins server that ensures every code update from GitHub is seamlessly deployed to your Tooling Website. This enhancement will streamline your deployment process, making it more efficient and reliable.

![image](https://github.com/user-attachments/assets/63215ebf-5bd0-4c14-a7e7-f3742a08e476)
