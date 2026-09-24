# Jenkins Controller-Agent Architecture & SonarQube Integration

> **Topic:** Jenkins Controller/Agent Architecture + SonarQube Integration
> **Practice:** Hands-on DevOps Learning
> **Environment:** AWS EC2, Amazon Linux, Java, Maven, Git

---

# 1. Project Overview

This project documents my hands-on practice with:

```text
Jenkins Controller/Agent Architecture
Jenkins Pipeline Agents
AWS EC2
Java
Maven
Git
SSH-Based Jenkins Agents
SonarQube
Jenkins + SonarQube Integration
```

The main objectives were:

```text
1. Understand Jenkins Controller/Agent architecture
2. Distribute build workloads across multiple machines
3. Configure EC2 instances as Jenkins agents
4. Connect Jenkins agents using SSH
5. Understand SonarQube code analysis
6. Integrate SonarQube with a Jenkins CI pipeline
```

---

# 2. Jenkins Controller & Agent Architecture

## What is Jenkins Controller?

The **Jenkins Controller** is the central Jenkins server that manages the Jenkins environment.

It is responsible for tasks such as:

* Managing jobs
* Scheduling builds
* Managing agents
* Managing credentials
* Managing Jenkins configuration
* Coordinating pipeline execution

---

# 3. What is a Jenkins Agent?

A **Jenkins Agent** is a machine that executes build and pipeline workloads assigned by the Jenkins Controller.

Agents can be separate EC2 instances or other machines connected to Jenkins.

---

# 4. Why Use Jenkins Agents?

When Jenkins handles many builds, running every workload directly on the controller can increase its workload.

Agents allow Jenkins to distribute workloads across multiple machines.

### Without Agents

```text
                 Jenkins Controller
                       |
             ┌─────────┼─────────┐
             ↓         ↓         ↓
           Build 1   Build 2   Build 3
             |
        High Workload
```

### With Agents

```text
                  Jenkins Controller
                         |
              ┌──────────┴──────────┐
              ↓                     ↓
           Agent 1                Agent 2
              |                     |
           Build 1               Build 2
              |                     |
           Build 3               Build 4
```

This allows workloads to be distributed across multiple machines.

---

# 5. Jenkins Controller-Agent Architecture

```text
                  +------------------------+
                  |   Jenkins Controller   |
                  |                        |
                  |  Manage & Schedule     |
                  |       Builds           |
                  +-----------+------------+
                              |
                    Jenkins Communication
                              |
              +---------------+---------------+
              |                               |
              ↓                               ↓
      +---------------+               +---------------+
      |    Agent 1    |               |    Agent 2    |
      |    slave1     |               |    slave2     |
      |               |               |               |
      | Java          |               | Java          |
      | Maven         |               | Maven         |
      | Git           |               | Git           |
      +---------------+               +---------------+
```

> Jenkins documentation increasingly uses **Controller** and **Agent** terminology instead of **Master** and **Slave**.

---

# 6. Jenkins Agent Requirements

An EC2 instance used as a Jenkins agent needs the required software and connectivity.

Typical requirements:

```text
Amazon Linux
Java
Maven
Git
SSH Connectivity
Jenkins Agent Configuration
```

The exact Java version should match the Jenkins and application requirements of the environment.

---

# 7. Agent EC2 Setup

For this practice, I created EC2 instances and configured them as Jenkins agents.

Example:

```text
Jenkins Controller
       |
       ├── Agent 1
       |
       └── Agent 2
```

Each agent can have the tools required to execute the assigned builds.

---

# 8. Install Java, Maven and Git

Example command:

```bash
sudo yum install java-21-amazon-corretto-devel maven git -y
```

Check Java:

```bash
java -version
```

Check Maven:

```bash
mvn --version
```

Check Git:

```bash
git --version
```

---

# 9. Configure Jenkins Agent

From the Jenkins Controller:

```text
Manage Jenkins
      ↓
Nodes
      ↓
New Node
      ↓
Permanent Agent
```

---

# 10. Agent Configuration

Example configuration:

```text
Node Name        : slave1

Executors        : 3

Remote Directory : /tmp

Labels           : slave1
```

---

# 11. What are Executors?

An **executor** represents a slot on an agent where Jenkins can run a build.

Example:

```text
Executors = 3
```

Conceptually:

```text
Agent 1
  |
  ├── Executor 1 → Build A
  ├── Executor 2 → Build B
  └── Executor 3 → Build C
```

The number of executors should be selected according to the resources and workload of the agent.

---

# 12. What are Labels?

Labels are used to identify specific Jenkins agents.

Example:

```text
Label:
slave1
```

A pipeline can use the label to request a specific agent.

Example:

```groovy
agent {
    label 'slave1'
}
```

This tells Jenkins to run the pipeline on an agent matching that label.

---

# 13. SSH Agent Configuration

The Jenkins Controller can connect to an EC2 agent using SSH.

Typical configuration:

```text
Launch Method
      ↓
Launch agents via SSH
```

### Host

```text
Private IP of Agent
```

### Credentials

```text
SSH Username with private key
```

### Username

```text
ec2-user
```

---

# 14. SSH Communication

The basic communication flow is:

```text
Jenkins Controller
        |
        | SSH
        ↓
Jenkins Agent
        |
        ↓
Execute Build
```

The Jenkins Controller manages the agent, while the agent performs the assigned build work.

---

# 15. Jenkins Credentials

SSH private keys should be stored securely in Jenkins C
