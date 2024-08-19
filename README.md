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

#### 5. Configure Jenkins to Copy Files to NFS Server

1. **Install "Publish Over SSH" Plugin**:
   - Go to "Manage Jenkins" > "Manage Plugins."
   - Search for "Publish Over SSH" in the "Available" tab and install it.

2. **Configure SSH Plugin**:
   - Go to "Manage Jenkins" > "Configure System."
   - Find the "Publish over SSH" section and configure:
     - **Private Key**: Use the `.pem` file for SSH access.
     - **Hostname**: Private IP of your NFS server.
     - **Username**: `ec2-user`.
     - **Remote Directory**: `/mnt/apps`.

3. **Test Configuration**:
   - Use the test option to ensure connectivity.

4. **Update Job Configuration**:
   - In your Jenkins project configuration, add "Send build artifacts over SSH" under "Post-build Actions."
   - Configure it to send all files to the remote directory `/mnt/apps`.

5. **Set Permissions on NFS Server**:
   ```bash
   sudo chown -R ec2-user:ec2-user /mnt/apps
   sudo chmod -R 777 /mnt/apps
   ```

6. **Run the Build**:
   - Trigger a new build from the Jenkins GUI to test the file transfer.

7. **Verify File Transfer**:
   - Check the `/mnt/apps` directory on your NFS server to confirm the files have been successfully copied.

### Conclusion

You have successfully set up Jenkins for continuous integration, configured it to transfer build artifacts to an NFS server, and validated the process.


