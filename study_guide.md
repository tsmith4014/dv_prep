# **Comprehensive Study Guide for Your DevOps and System Administrator Internship**

---

## **Table of Contents**

1. **Introduction**
2. **Linux Internals**
   - a. sysctl
   - b. NUMA (Non-Uniform Memory Access)
   - c. GNU Readline
3. **Git Proficiency**
   - a. Cloning Repositories with Submodules
   - b. Forking and Making Changes
   - c. Merging Back and Updating Upstream Submodules
4. **Ansible**
   - a. Fundamentals
   - b. On-Premises Configuration Management
   - c. Advanced Concepts
5. **Terraform**
   - a. Basics
   - b. AWS Infrastructure Automation
   - c. Best Practices
6. **Oracle Cloud and OKE Cluster with Cluster API**
   - a. Oracle Cloud Setup
   - b. Kubernetes Cluster Management with Cluster API
   - c. Practical Exercises
7. **Additional Resources**
8. **Conclusion**

---

## **1. Introduction**

Welcome to your comprehensive study guide designed to prepare you for your upcoming DevOps and System Administrator internship at DV Trading in Chicago. This guide covers essential topics recommended by your future team, focusing on Linux internals, Git proficiency, configuration management with Ansible, infrastructure automation with Terraform, and Kubernetes cluster management using Oracle Cloud's OKE and Cluster API.

---

## **2. Linux Internals**

Understanding Linux internals is crucial for system administration, especially in environments that demand low latency and high performance like trading systems.

### **a. sysctl**

#### **Overview**

`sysctl` is a powerful tool that allows you to view and modify kernel parameters at runtime. These parameters can control various aspects of system behavior, including networking, process management, and hardware configurations.

#### **Key Concepts**

- **Kernel Parameters**: Variables that control the operation of the kernel.
- **/proc/sys/**: The directory where kernel parameters can be read and modified.
- **Persistence**: Changes made via `sysctl` are not persistent across reboots unless configured.

#### **Commands and Usage**

- **View All Parameters**:

  ```bash
  sysctl -a
  ```

- **Get Specific Parameter**:

  ```bash
  sysctl security.mac.asp.stats.bastion_upcall_count
  ```

- **Set Parameter Temporarily**:

  ```bash
  sysctl -w net.ipv4.ip_forward=1
  ```

- **Set Parameter Permanently**:

  Add the parameter to `/etc/sysctl.conf` or a `.conf` file inside `/etc/sysctl.d/`.

  ```bash
  echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
  sudo sysctl -p
  ```

#### **Practical Exercises**

1. **Modify Network Parameters**: Enable IP forwarding to allow your system to forward packets.

   ```bash
   sudo sysctl -w net.ipv4.ip_forward=1
   ```

2. **Optimize Memory Usage**: Adjust the `vm.swappiness` parameter to control the tendency of the kernel to swap.

   ```bash
   sudo sysctl -w vm.swappiness=10
   ```

3. **Enhance File Descriptors Limit**: Increase the maximum number of open files.

   ```bash
   sudo sysctl -w fs.file-max=100000
   ```

#### **Considerations**

- Always back up current settings before making changes.
- Understand the impact of each parameter, especially in production environments.
- Use caution when modifying parameters related to memory and networking.

### **b. NUMA (Non-Uniform Memory Access)**

#### **Overview**

NUMA is a memory architecture used in multiprocessing where memory access time depends on the memory location relative to a processor. It optimizes memory performance by taking advantage of the processor's proximity to memory.

#### **Key Concepts**

- **NUMA Nodes**: Groupings of CPUs and memory that are closer to each other.
- **Local vs. Remote Memory Access**: Accessing local memory is faster than remote memory.
- **numactl**: A tool to control NUMA policy for processes.

#### **Commands and Usage**

- **View NUMA Configuration**:

  ```bash
  numactl --hardware
  ```

- **Run a Process on Specific NUMA Node**:

  ```bash
  numactl --cpunodebind=0 --membind=0 ./your_application
  ```

- **Check NUMA Policy of a Process**:

  ```bash
  numastat -p $(pidof your_application)
  ```

#### **Practical Exercises**

1. **Inspect NUMA Nodes**: Identify how many NUMA nodes your system has.

   ```bash
   lscpu | grep -i numa
   ```

2. **Run Memory-Intensive Application**: Use `numactl` to bind a memory-intensive application to a specific NUMA node and observe performance.

3. **Monitor NUMA Statistics**: Use `numastat` to monitor NUMA statistics while running applications.

#### **Considerations**

- NUMA effects are more pronounced in systems with multiple processors.
- Binding processes to specific NUMA nodes can improve performance for low-latency applications.
- Be cautious as improper configurations can lead to performance degradation.

### **c. GNU Readline**

#### **Overview**

GNU Readline is a software library that provides line-editing and history capabilities for interactive programs with a command-line interface, such as Bash.

#### **Key Concepts**

- **Command-Line Editing**: Use of keyboard shortcuts to navigate and edit the command line efficiently.
- **History Navigation**: Accessing and reusing previous commands.
- **Customization**: Modifying Readline behavior through the `~/.inputrc` file.

#### **Common Keyboard Shortcuts**

- **Navigation**:

  - `Ctrl + a`: Move to the beginning of the line.
  - `Ctrl + e`: Move to the end of the line.
  - `Alt + f`: Move forward one word.
  - `Alt + b`: Move backward one word.

- **Editing**:

  - `Ctrl + k`: Cut text from the cursor to the end of the line.
  - `Ctrl + u`: Cut text from the cursor to the beginning of the line.
  - `Ctrl + y`: Paste (yank) the last cut text.
  - `Ctrl + w`: Cut the word before the cursor.

- **History**:
  - `Ctrl + r`: Reverse search through command history.
  - `Ctrl + p`: Previous command.
  - `Ctrl + n`: Next command.

#### **Customization**

- **Modify `~/.inputrc`**:

  ```bash
  # Enable vi editing mode
  set editing-mode vi

  # Customize key bindings
  "\e[1~": beginning-of-line
  "\e[4~": end-of-line
  ```

#### **Practical Exercises**

1. **Practice Shortcuts**: Spend time using keyboard shortcuts instead of the mouse or arrow keys.

2. **Customize Your Shell**: Modify your `~/.inputrc` to change key bindings or enable vi/emacs mode.

3. **Use Macros**: Create macros in `~/.inputrc` for frequently used commands.

#### **Considerations**

- Mastering Readline can significantly speed up command-line work.
- Consistent practice is key to internalizing shortcuts.
- Customizations can be system-specific; ensure portability if working across multiple systems.

---

## **3. Git Proficiency**

Git is an essential tool for version control in software development. Proficiency in Git workflows, especially with submodules and collaborative features, is crucial.

### **a. Cloning Repositories with Submodules**

#### **Overview**

Submodules allow you to include and manage repositories within a repository, enabling modular project structures.

#### **Key Concepts**

- **Submodule**: A Git repository embedded inside another Git repository.
- **`.gitmodules`**: A configuration file that keeps track of submodule repositories.

#### **Commands and Usage**

- **Clone a Repository with Submodules**:

  ```bash
  git clone --recurse-submodules <repository_url>
  ```

- **Initialize and Update Submodules (if already cloned)**:

  ```bash
  git submodule init
  git submodule update
  ```

- **Add a Submodule**:

  ```bash
  git submodule add <submodule_repo_url> <path>
  ```

#### **Practical Exercises**

1. **Clone with Submodules**: Clone a repository that uses submodules and ensure all submodules are initialized.

2. **Explore `.gitmodules`**: Open the `.gitmodules` file to understand how submodules are configured.

3. **Update Submodules**: Practice pulling updates from submodules.

#### **Considerations**

- Always use `--recurse-submodules` when cloning to ensure submodules are included.
- Be aware of the submodule's commit state; they can point to specific commits, not necessarily the latest.

### **b. Forking and Making Changes**

#### **Overview**

Forking allows you to create your own copy of a repository to make changes without affecting the original project.

#### **Key Concepts**

- **Fork**: A personal copy of someone else's repository.
- **Pull Request**: A request to merge your changes back into the original repository.
- **Upstream Repository**: The original repository from which you forked.

#### **Commands and Usage**

- **Fork a Repository**: Use the GitHub or GitLab interface to fork.

- **Clone Your Fork**:

  ```bash
  git clone <your_forked_repo_url>
  ```

- **Set Upstream Remote**:

  ```bash
  git remote add upstream <original_repo_url>
  ```

- **Fetch and Merge Upstream Changes**:

  ```bash
  git fetch upstream
  git merge upstream/main
  ```

#### **Practical Exercises**

1. **Fork and Clone**: Fork a repository and clone it to your local machine.

2. **Make Changes**: Make code changes, add features, or fix bugs.

3. **Commit and Push**:

   ```bash
   git add .
   git commit -m "Your commit message"
   git push origin main
   ```

4. **Create a Pull Request**: Use the platform's interface to submit a pull request to the original repository.

#### **Considerations**

- Keep your fork updated with the upstream repository to minimize merge conflicts.
- Write clear and descriptive commit messages.
- Follow the contribution guidelines of the original repository.

### **c. Merging Back and Updating Upstream Submodules**

#### **Overview**

After making changes in your fork, you may want to merge them back into the upstream repository and update any submodules accordingly.

#### **Key Concepts**

- **Merge Conflicts**: Occur when changes in different branches affect the same lines of code.
- **Submodule Update**: Ensuring submodules point to the correct commits after merging.

#### **Commands and Usage**

- **Merge Changes into Upstream**:

  After your pull request is accepted, sync your fork:

  ```bash
  git fetch upstream
  git checkout main
  git merge upstream/main
  git push origin main
  ```

- **Update Submodules to Latest Commit**:

  ```bash
  git submodule update --remote
  ```

- **Commit Submodule Changes**:

  ```bash
  git add path_to_submodule
  git commit -m "Update submodule to latest commit"
  git push origin main
  ```

#### **Practical Exercises**

1. **Handle Merge Conflicts**: Intentionally create a conflict to practice resolving it.

2. **Update Submodules**: Change something in the submodule repository, update it in the main repository, and push the changes.

3. **Review Submodule Status**:

   ```bash
   git submodule status
   ```

#### **Considerations**

- Be cautious when updating submodules; ensure compatibility with the main project.
- Document any changes to submodules for team awareness.
- Understand that submodules can introduce complexity in the workflow.

---

## **4. Ansible**

Ansible is a powerful automation tool for configuration management, application deployment, and task automation.

### **a. Fundamentals**

#### **Overview**

Ansible uses a simple, agentless architecture and employs YAML for its playbooks, making it accessible and easy to use.

#### **Key Concepts**

- **Control Node**: The machine where Ansible is installed.
- **Managed Nodes**: The devices or servers Ansible manages.
- **Inventory**: A list of managed nodes.
- **Modules**: Units of work that Ansible executes.
- **Playbooks**: YAML files that define a series of tasks.

#### **Installation**

- **On Ubuntu/Debian**:

  ```bash
  sudo apt update
  sudo apt install ansible
  ```

- **On CentOS/RHEL**:

  ```bash
  sudo yum install ansible
  ```

### **b. On-Premises Configuration Management**

#### **Inventory Management**

- **Static Inventory**: Defined in a simple text file.

  ```ini
  [webservers]
  server1.example.com
  server2.example.com

  [dbservers]
  server3.example.com
  ```

- **Dynamic Inventory**: Generated by scripts for environments that change frequently.

#### **Ad-Hoc Commands**

- **Ping All Servers**:

  ```bash
  ansible all -m ping
  ```

- **Execute Command on Group**:

  ```bash
  ansible webservers -a "/bin/echo hello"
  ```

#### **Writing Playbooks**

- **Structure**:

  ```yaml
  - name: Description of the play
    hosts: webservers
    become: yes
    tasks:
      - name: Install Nginx
        apt:
          name: nginx
          state: present
  ```

#### **Common Modules**

- **Package Management**: `apt`, `yum`
- **Service Management**: `service`, `systemd`
- **File Management**: `copy`, `template`, `file`

#### **Variables and Facts**

- **Variables**: Defined in playbooks or separate vars files.

  ```yaml
  vars:
    http_port: 80
  ```

- **Facts**: Information gathered about the managed nodes.

  Accessed using `ansible_facts`.

#### **Loops and Conditionals**

- **Loops**:

  ```yaml
  tasks:
    - name: Install multiple packages
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - curl
        - vim
  ```

- **Conditionals**:

  ```yaml
  tasks:
    - name: Only run when on Ubuntu
      apt:
        name: apache2
        state: present
      when: ansible_facts['os_family'] == 'Debian'
  ```

#### **Roles and Galaxy**

- **Roles**: Structured way to organize playbooks.

  Directory structure:

  ```
  roles/
    common/
      tasks/
      handlers/
      templates/
      files/
      vars/
      defaults/
      meta/
  ```

- **Ansible Galaxy**: A repository of roles.

  Install a role:

  ```bash
  ansible-galaxy install username.rolename
  ```

### **c. Advanced Concepts**

#### **Ansible Vault**

- **Encrypt sensitive data**:

  ```bash
  ansible-vault encrypt vars/secrets.yml
  ```

- **Use encrypted files in playbooks**:

  ```yaml
  vars_files:
    - vars/secrets.yml
  ```

#### **Dynamic Inventory Scripts**

- **Example with AWS EC2**:

  Use the `ec2.py` script provided by Ansible to generate an inventory based on AWS instances.

#### **Error Handling and Debugging**

- **Ignore Errors**:

  ```yaml
  tasks:
    - name: Attempt to install package
      apt:
        name: nonexistent-package
        state: present
      ignore_errors: yes
  ```

- **Debug Module**:

  ```yaml
  tasks:
    - name: Display a variable
      debug:
        var: http_port
  ```

#### **Practical Exercises**

1. **Set Up a Web Server**: Write a playbook to install and configure a web server.

2. **Create a Role**: Organize your web server setup into a reusable role.

3. **Encrypt Variables**: Use Ansible Vault to secure sensitive data like passwords or API keys.

4. **Use Dynamic Inventory**: Configure a dynamic inventory for AWS or another cloud provider.

#### **Considerations**

- Keep playbooks idempotent to ensure they can be run multiple times without unintended effects.
- Use version control for your Ansible codebase.
- Test playbooks in a staging environment before deploying to production.

---

## **5. Terraform**

Terraform is an infrastructure as code (IaC) tool that allows you to define and provision data center infrastructure using declarative configuration files.

### **a. Basics**

#### **Overview**

Terraform enables you to manage infrastructure across multiple cloud providers through a consistent workflow.

#### **Key Concepts**

- **Providers**: Plugins that enable interaction with APIs of cloud platforms.
- **Resources**: Components managed by Terraform (e.g., virtual networks, compute instances).
- **State File**: Keeps track of the resources Terraform manages.
- **Variables**: Input parameters for configurations.

#### **Installation**

- Download the appropriate package from the [official website](https://www.terraform.io/downloads) and add it to your system's PATH.

### **b. AWS Infrastructure Automation**

#### **AWS Setup**

- **Create AWS Account**: Sign up for the [AWS Free Tier](https://aws.amazon.com/free/).
- **Configure Credentials**:

  ```bash
  aws configure
  ```

#### **Writing Terraform Configurations**

- **Main Configuration File**: Typically named `main.tf`.

  ```hcl
  provider "aws" {
    region = "us-east-1"
  }

  resource "aws_instance" "example" {
    ami           = "ami-0c94855ba95c71c99"
    instance_type = "t2.micro"

    tags = {
      Name = "TerraformExample"
    }
  }
  ```

#### **Commands and Workflow**

- **Initialize the Working Directory**:

  ```bash
  terraform init
  ```

- **Format Configuration Files**:

  ```bash
  terraform fmt
  ```

- **Validate Configuration Files**:

  ```bash
  terraform validate
  ```

- **Plan Changes**:

  ```bash
  terraform plan
  ```

- **Apply Changes**:

  ```bash
  terraform apply
  ```

- **Destroy Resources**:

  ```bash
  terraform destroy
  ```

#### **Variables and Outputs**

- **Define Variables**:

  ```hcl
  variable "instance_type" {
    description = "Type of EC2 instance"
    default     = "t2.micro"
  }
  ```

- **Use Variables**:

  ```hcl
  resource "aws_instance" "example" {
    ami           = var.ami_id
    instance_type = var.instance_type
  }
  ```

- **Outputs**:

  ```hcl
  output "instance_ip" {
    value = aws_instance.example.public_ip
  }
  ```

#### **State Management**

- **State File Location**: By default, stored in `terraform.tfstate`.

- **Remote State Storage**: Store state files remotely for team collaboration.

  Example using AWS S3:

  ```hcl
  terraform {
    backend "s3" {
      bucket = "my-terraform-state"
      key    = "global/s3/terraform.tfstate"
      region = "us-east-1"
    }
  }
  ```

### **c. Best Practices**

#### **Use Modules**

- **Create Reusable Modules**: Organize resources into modules for reusability.

  Directory structure:

  ```
  modules/
    vpc/
      main.tf
      variables.tf
      outputs.tf
  ```

- **Call Modules**:

  ```hcl
  module "vpc" {
    source = "./modules/vpc"
    cidr_block = "10.0.0.0/16"
  }
  ```

#### **Environment Management**

- **Workspaces**: Manage multiple environments (e.g., dev, staging, prod) within the same configuration.

  ```bash
  terraform workspace new dev
  terraform workspace select dev
  ```

#### **Version Control**

- **Lock Provider Versions**:

  ```hcl
  provider "aws" {
    version = "~> 3.0"
  }
  ```

- **Use `.gitignore`**: Exclude files like `terraform.tfstate` and `.terraform/` directories.

#### **Collaboration**

- **Remote State Locking**: Prevents concurrent modifications.

- **Documentation**: Comment your configurations and maintain a README.

#### **Practical Exercises**

1. **Provision an EC2 Instance**: Write a Terraform configuration to launch an EC2 instance with a security group.

2. **Create a VPC**: Use a module to set up a Virtual Private Cloud with subnets and routing.

3. **Implement Remote State**: Configure remote state storage using AWS S3.

4. **Use Variables and Outputs**: Parameterize your configurations and output useful information.

#### **Considerations**

- Be cautious with resource deletion; Terraform destroy is irreversible.
- Regularly backup your state files.
- Use Terraform Cloud or Enterprise for team collaboration and enhanced features.

---

## **6. Oracle Cloud and OKE Cluster with Cluster API**

While DV Trading doesn't use Oracle Cloud, setting up an OKE cluster with Cluster API is a valuable exercise to understand Kubernetes cluster management.

### **a. Oracle Cloud Setup**

#### **Sign Up**

- Create a free account on [Oracle Cloud](https://www.oracle.com/cloud/free/).

#### **Familiarize with the Console**

- **Navigation**: Explore services like Compute, Networking, and OKE.
- **API Keys**: Generate API keys for CLI and API access.

#### **Install OCI CLI**

- **Installation**:

  ```bash
  bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
  ```

- **Configuration**:

  ```bash
  oci setup config
  ```

### **b. Kubernetes Cluster Management with Cluster API**

#### **Overview of Cluster API (CAPI)**

Cluster API provides declarative APIs and tooling to simplify provisioning, upgrading, and operating multiple Kubernetes clusters.

#### **Prerequisites**

- **kubectl**: Install and configure `kubectl`.

- **clusterctl**: The Cluster API CLI tool.

  Install via `clusterctl`:

  ```bash
  curl -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.2.0/clusterctl-linux-amd64 -o clusterctl
  chmod +x clusterctl
  sudo mv clusterctl /usr/local/bin/
  ```

#### **Initialize Cluster API**

- **Set Up Infrastructure Provider**:

  ```bash
  clusterctl init --infrastructure oci
  ```

- **Configuration Files**: Customize the provider configurations as needed.

### **c. Practical Exercises**

#### **Create a Management Cluster**

- **Using Kind or Minikube**:

  ```bash
  kind create cluster
  ```

- **Initialize the Management Cluster**:

  ```bash
  clusterctl init --infrastructure oci
  ```

#### **Provision a Workload Cluster**

- **Define Cluster Configuration**:

  Generate templates:

  ```bash
  clusterctl generate cluster my-cluster --infrastructure oci > my-cluster.yaml
  ```

- **Apply Configuration**:

  ```bash
  kubectl apply -f my-cluster.yaml
  ```

- **Monitor Cluster Creation**:

  ```bash
  kubectl get clusters
  ```

#### **Manage the Cluster**

- **Scaling**: Adjust the number of worker nodes.

  ```yaml
  # In my-cluster.yaml, update the replicas count
  replicas: 3
  ```

  Apply changes:

  ```bash
  kubectl apply -f my-cluster.yaml
  ```

- **Upgrade Kubernetes Version**: Modify the Kubernetes version in your configuration and apply.

#### **Delete the Cluster**

- **Cleanup Resources**:

  ```bash
  kubectl delete cluster my-cluster
  ```

#### **Considerations**

- **Costs**: Be mindful of resource usage to avoid unexpected charges.
- **Permissions**: Ensure your OCI user has the necessary permissions to create and manage resources.
- **Security**: Secure API keys and configuration files.

---

## **7. Additional Resources**

- **Linux**

  - _The Linux Programming Interface_ by Michael Kerrisk
  - _Linux Kernel Documentation_: https://www.kernel.org/doc/html/latest/

- **Git**

  - _Pro Git_ by Scott Chacon and Ben Straub: https://git-scm.com/book/en/v2
  - Git Immersion Tutorial: https://gitimmersion.com/

- **Ansible**

  - _Ansible for DevOps_ by Jeff Geerling
  - Official Documentation: https://docs.ansible.com/

- **Terraform**

  - Terraform Tutorials: https://learn.hashicorp.com/terraform
  - _Terraform: Up and Running_ by Yevgeniy Brikman

- **Kubernetes and Cluster API**

  - Official Kubernetes Documentation: https://kubernetes.io/docs/home/
  - Cluster API Book: https://cluster-api.sigs.k8s.io/user/quick-start.html

- **Oracle Cloud**

  - OCI Documentation: https://docs.oracle.com/en-us/iaas/Content/home.htm
  - OKE Documentation: https://docs.oracle.com/en-us/iaas/Content/ContEng/Concepts/contengoverview.htm

---

## **8. Conclusion**

This study guide provides a structured approach to mastering the essential tools and concepts you'll need for your upcoming internship. Focus on hands-on practice, as real-world application solidifies theoretical knowledge. Don't hesitate to explore beyond the guide, as the field of DevOps is vast and continuously evolving.

**Best of luck with your preparations and your internship at DV Trading!**

---
