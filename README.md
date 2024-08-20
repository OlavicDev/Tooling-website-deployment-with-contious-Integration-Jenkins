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



### Step 1: Set Up Jenkins Server

#### 1. Launch an EC2 Instance

1. **Sign In to AWS Management Console**:
   - Go to the [AWS Management Console](https://aws.amazon.com/console/).

2. **Navigate to EC2 Dashboard**:
   - Select "EC2" from the services menu.

3. **Launch Instance**:
   - Click on "Launch Instance."
   - Choose "Ubuntu Server 20.04 LTS" as the operating system.

4. **Configure Instance Details**:
   - Choose the instance type, network settings, subnet, and any additional options according to your needs.

5. **Add Storage**:
   - Allocate storage based on your requirements.

6. **Add Tags** (Optional):
   - Add tags for better organization.

7. **Configure Security Group**:
   - Create a new security group or use an existing one.
   - Allow inbound traffic on ports 22 (SSH), 80 (HTTP), and 443 (HTTPS) from your IP address.

8. **Review and Launch**:
   - Review your configuration and launch the instance.

![image](https://github.com/user-attachments/assets/8363b0c0-beac-4f63-beff-aef0464f8b60)

#### 2. Install JDK

Once your EC2 instance is running:

1. **Connect to Your EC2 Instance**:
   - Use SSH to connect to your instance. For example:
     ```
     ssh -i your-key.pem ubuntu@your-ec2-public-ip
     ```

2. **Update Package List**:
   ```
   sudo apt update
   ```

3. **Install Default JDK**:
   ```
   sudo apt install default-jdk-headless
   ```

#### 3. Install Jenkins

1. **Add Jenkins Repository Key**:
   ```
   wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
   ```

2. **Add Jenkins Repository**:
   ```
   sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
   ```

3. **Update Package List**:
   ```
   sudo apt update
   ```

4. **Install Jenkins**:
   ```
   sudo apt-get install jenkins
   ```

5. **Check Jenkins Status**:
   ```
   sudo systemctl status jenkins
   ```
![image](https://github.com/user-attachments/assets/7e5d2574-7d2e-436e-a605-704677ee63a2)

6. **Open Jenkins Port (8080)**:
   - Update your EC2 Security Group to allow inbound traffic on port 8080.
![image](https://github.com/user-attachments/assets/f5e31c61-7c60-41f7-9a0c-ea2249a7eda0)

#### 4. Initial Jenkins Setup

1. **Access Jenkins**:
   - Open your browser and go to `http://your-ec2-public-ip:8080`.
![image](https://github.com/user-attachments/assets/8cd7a796-35df-4d6e-af58-c090eb1d915c)

2. **Retrieve Initial Admin Password**:
   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

3. **Install Suggested Plugins**:
   - Follow the prompts on the Jenkins setup page.

4. **Create an Admin Account**:
   - Set up your Jenkins admin account as guided.
![image](https://github.com/user-attachments/assets/3c6acc72-a9c7-46ac-9d70-bf569ed140d5)

5. **Create a Freestyle Project**:
   - Click "New Item" and select "Freestyle project."

6. **Configure GitHub Repository**:
   - In the project configuration, select "Git" and enter your GitHub repository URL and credentials.

7. **Run Build**:
   - Click "Build Now" to start the build process.

8. **Configure GitHub Webhook**:
   - Set up GitHub webhooks to trigger builds on code changes.

9. **Add Post-Build Actions**:
   - Configure post-build actions to archive build artifacts.
![image](https://github.com/user-attachments/assets/3e08fe0d-a18f-49b3-bee4-bc5aad928d93)

we have now configured an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as 'push' because the changes are being 'pushed' and files transfer is initiated by GitHub) By default, the artifacts are stored on Jenkins server locally
```
ls -lrt /var/lib/jenkins/jobs/tooling/builds/3/archive/
```
![image](https://github.com/user-attachments/assets/b792927e-1de3-4fed-80a2-22791b83874e)


#### 5. Configure Jenkins to Copy Files to NFS Server

1. **Install "Publish Over SSH" Plugin**:
   - Go to "Manage Jenkins" > "Manage Plugins."
   - Search for "Publish Over SSH" in the "Available" tab and install it.
![image](https://github.com/user-attachments/assets/08199c39-4625-4ec2-9cbb-f1906681ab13)

2. **Configure SSH Plugin**:
   - Go to "Manage Jenkins" > "Configure System."
   - Find the "Publish over SSH" section and configure:
     - **Private Key**: Use the `.pem` file for SSH access.
     - **Hostname**: Private IP of your NFS server.
     - **Username**: `ec2-user`.
     - **Remote Directory**: `/mnt/apps`.

3. **Test Configuration**:
   - Use the test option to ensure connectivity.
![image](https://github.com/user-attachments/assets/565f4101-66c1-4132-a05c-f6c50bffb337)

4. **Update Job Configuration**:
   - In your Jenkins project configuration, add "Send build artifacts over SSH" under "Post-build Actions."
   - Configure it to send all files to the remote directory `/mnt/apps`.
![image](https://github.com/user-attachments/assets/1c7d2e7f-936c-48f1-ab99-cf490e8f5e3b)

5. **Set Permissions on NFS Server**:
   ```
   sudo chown -R ec2-user:ec2-user /mnt/apps
   sudo chmod -R 777 /mnt/apps
   ```

6. **Run the Build**:
   - Trigger a new build from the Jenkins GUI to test the file transfer.

7. **Verify File Transfer**:
   - Check the `/mnt/apps` directory on your NFS server to confirm the files have been successfully copied.
![image](https://github.com/user-attachments/assets/489948c0-47f5-4257-9fe5-d6ad307f0ef6)


You have successfully set up Jenkins for continuous integration, configured it to transfer build artifacts to an NFS server, and validated the process.

### Overview of the Project

**Project Title:** Tooling Website Deployment with Continuous Integration: Introduction to Jenkins

**Objective:** 
The project aims to implement a Continuous Integration (CI) system using Jenkins, an open-source automation server. This system will automate the deployment of updates to the Tooling website from a GitHub repository to an NFS server. By doing so, it streamlines the process of integrating and deploying code changes, ensuring that the website is always up-to-date with the latest modifications.

### Reason for the Project

In modern software development, the speed of delivery and the quality of code are crucial. Continuous Integration (CI) is a development practice that addresses these needs by automating the process of integrating code changes from multiple contributors into a shared repository. This project is essential for several reasons:

1. **Automation of Repetitive Tasks:** By using Jenkins to automate the deployment process, the team can focus on development rather than manual deployment tasks. This reduces human error and increases efficiency.

2. **Faster Development Cycles:** With CI in place, code changes are automatically tested and deployed, reducing the time it takes to get updates into production. This allows for more frequent releases and quicker responses to issues or feature requests.

3. **Improved Code Quality:** Jenkins can be configured to run automated tests and checks on every code commit. This ensures that only high-quality, bug-free code is deployed to production.

4. **Centralized Management:** Jenkins provides a centralized platform for managing the build and deployment processes. This makes it easier to track changes, monitor the status of deployments, and manage project configurations.

### Importance of the Project

1. **Scalability and Flexibility:** As the project and team grow, Jenkins can easily scale to manage more complex builds, deployments, and integrations. It supports a wide range of plugins and tools, making it adaptable to various project needs.

2. **Enhanced Collaboration:** Continuous Integration fosters collaboration among developers. Since all changes are integrated frequently, developers can work in parallel, with confidence that their changes will not break the project.

3. **Reliability and Consistency:** By automating the deployment process, Jenkins ensures that every deployment is consistent and reliable. This reduces the risk of deployment errors and ensures that the production environment is always in a known state.

4. **Increased Transparency:** Jenkins provides detailed logs and reports for every build and deployment, making it easy to track changes, diagnose issues, and understand the state of the project at any given time.

5. **Future-Proofing:** Implementing a CI system like Jenkins prepares the project for future expansion and complexity. As more features are added, the system can adapt to handle more intricate workflows without compromising on speed or quality.

### Conclusion

This project is a crucial step in modernizing the development workflow for the Tooling website. By integrating Jenkins as a CI tool, the team ensures that the website is always up-to-date with the latest code changes, delivered quickly and reliably. This not only improves the development process but also enhances the overall quality and stability of the website, providing a better experience for end-users.


