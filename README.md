# Jenkins-Master-Slave-Architecture-SonarQube-Integration

**📌 Overview**

This project documents my DevOps learning and hands-on practice with:

Jenkins Master/Slave (Controller/Agent) architecture
Jenkins Pipeline agents
AWS EC2
Java
Maven
Git
SSH-based Jenkins agents
SonarQube
Jenkins + SonarQube integration

The objective was to understand how Jenkins can distribute build workloads across multiple machines and how SonarQube can be integrated into a CI pipeline for code-quality analysis.

**1. Jenkins Master & Slave Architecture**
Why use Jenkins Agents?

When Jenkins handles many builds, executing all workloads on the Jenkins controller can increase its workload.

Jenkins agents can be used to distribute build jobs across multiple machines.

**Architecture**
                    +----------------------+
                    |   Jenkins Controller |
                    |       / Master       |
                    +----------+-----------+
                               |
                +--------------+--------------+
                |                             |
                v                             v
       +----------------+            +----------------+
       |    Agent 1     |            |    Agent 2     |
       |    slave1      |            |    slave2      |
       |                |            |                |
       | Java/Maven/Git  |            | Java/Maven/Git |
       +----------------+            +----------------+

Jenkins documentation increasingly uses Controller and Agent terminology instead of Master/Slave.

**2. Agent Setup**

I practiced creating EC2 instances and configuring them as Jenkins agents.

Agent requirements
Amazon Linux
Java
Maven
Git
SSH connectivity
Jenkins agent configuration

Example installation:

sudo yum install java-21-amazon-corretto-devel maven git -y

The required Java version should match the Jenkins/plugin/application requirements used in the environment.

**3. Configure Jenkins Node**

From Jenkins:

Manage Jenkins
      ↓
Nodes
      ↓
New Node
      ↓
Permanent Agent

Example configuration:

Node Name       : slave1
Executors       : 3
Remote Directory: /tmp
Labels          : slave1

The label can be used to assign specific workloads to a particular agent.

**4. SSH Agent Configuration**

The Jenkins controller can connect to the EC2 agent through SSH.

Typical configuration includes:

Launch method:
Launch agents via SSH

Host:
Private IP of Agent

Credentials:
SSH Username with private key

Username:
ec2-user

Private keys and credentials should never be committed to GitHub.




**5. Monitoring Jenkins Agents**

Jenkins provides monitoring information for configured nodes.

Useful information includes:

Load statistics
System information
Build history
Executor status
Agent availability

This helps understand how workloads are being distributed.

**6.SonarQube**
What is SonarQube?

SonarQube is a code-quality and code-security analysis platform.

It can help identify:

Bugs
Code smells
Duplicate code
Security vulnerabilities
Other code-quality issues

SonarQube can be integrated into a Jenkins CI pipeline so that source code is analyzed during the build process.

**7.SonarQube Setup**

I practiced setting up SonarQube on a separate EC2 instance.

The environment included:

AWS EC2
    ↓
Amazon Linux
    ↓
Java
    ↓
SonarQube
    ↓
Port 9000

After installation, SonarQube can be accessed through:

http://<SONARQUBE-IP>:9000

Never publish real server IP addresses, credentials or authentication tokens in a public repository.


**8.Jenkins + SonarQube Integration**

The Jenkins environment can be configured with the required SonarQube plugins and server configuration.

Typical components include:

SonarQube Scanner
Maven Integration
SonarQube Quality Gates

The SonarQube server is then configured in Jenkins under:

Manage Jenkins
    ↓
System
    ↓
SonarQube Servers

Authentication tokens should be stored securely in Jenkins Credentials rather than directly inside the Jenkinsfile.


**Pipeline Flow**
Developer
    |
    v
Git Repository
    |
    v
Jenkins
    |
    +----> Compile
    |
    +----> Test
    |
    +----> SonarQube Analysis
    |
    +----> Package
    |
    v
Artifact
