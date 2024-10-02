# **Comprehensive Study Guide for Your DevOps and System Administrator Internship**

---

## **Table of Contents**

1. **Introduction**
2. **System Internals**
   - a. sysctl
   - b. NUMA (Non-Uniform Memory Access)
   - c. Command-Line Efficiency (GNU Readline, Shell Enhancements)
3. **Git Proficiency**
   - a. Cloning Repositories with Submodules
   - b. Forking and Making Changes
   - c. Merging Back and Updating Upstream Submodules
4. **Ansible**
   - a. Installation
   - b. Configuration Management
   - c. Advanced Concepts
5. **Terraform**
   - a. Installation
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

This study guide is designed to prepare you for your upcoming DevOps and System Administrator internship at high-speed trading firm in Chicago. It covers essential topics recommended by your future team, focusing on both macOS and Linux environments to align with the hybrid infrastructure you'll encounter. The guide emphasizes practical, hands-on exercises to ensure you're comfortable working in both operating systems.

---

## **2. System Internals**

Understanding system internals is crucial for system administration, especially in environments demanding high performance and low latency.

### **a. sysctl**

#### **Overview**

`sysctl` allows you to view and modify kernel parameters at runtime, affecting system behavior related to networking, security, and hardware.

#### **Key Concepts**

- **Kernel Parameters**: Variables controlling system behavior.
- **Persistence**: Changes made via `sysctl` are temporary unless made persistent.

#### **macOS vs. Linux Differences**

- **macOS**: Uses BSD-style `sysctl`.
- **Linux**: Uses GNU-style `sysctl`.

#### **Commands and Usage**

**Viewing All Parameters**

- **macOS**:

  ```bash
  sysctl -a
  ```

- **Linux**:

  ```bash
  sysctl -a
  ```

**Getting Specific Parameter**

- **macOS**:

  ```bash
  sysctl net.inet.ip.forwarding
  ```

- **Linux**:

  ```bash
  sysctl net.ipv4.ip_forward
  ```

**Setting Parameter Temporarily**

- **macOS**:

  ```bash
  sudo sysctl -w net.inet.ip.forwarding=1
  ```

- **Linux**:

  ```bash
  sudo sysctl -w net.ipv4.ip_forward=1
  ```

**Setting Parameter Permanently**

- **macOS**:

  - macOS doesn't support `/etc/sysctl.conf` by default.
  - Use launch daemons or scripts at startup.

- **Linux**:

  ```bash
  echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
  sudo sysctl -p
  ```

#### **Practical Exercises**

1. **Enable IP Forwarding**

   - **macOS**:

     ```bash
     sudo sysctl -w net.inet.ip.forwarding=1
     sysctl net.inet.ip.forwarding
     ```

   - **Linux**:

     ```bash
     sudo sysctl -w net.ipv4.ip_forward=1
     sysctl net.ipv4.ip_forward
     ```

2. **List Network Parameters**

   - **macOS**:

     ```bash
     sysctl net.inet
     ```

   - **Linux**:

     ```bash
     sysctl net.ipv4
     ```

3. **Adjust Maximum Files Open Limit**

   - **macOS**:

     ```bash
     sysctl kern.maxfiles
     sudo sysctl -w kern.maxfiles=65536
     ```

   - **Linux**:

     ```bash
     sysctl fs.file-max
     sudo sysctl -w fs.file-max=65536
     ```

#### **Considerations**

- **Safety**: Always understand the impact of kernel parameter changes.
- **Persistence**: Linux supports persistent changes via `/etc/sysctl.conf`; macOS requires alternative methods.
- **Permissions**: `sudo` is often required.

### **b. NUMA (Non-Uniform Memory Access)**

#### **Overview**

NUMA optimizes memory access times in multi-processor systems. While macOS hardware generally doesn't require NUMA tuning, Linux systems, especially servers, often benefit from it.

#### **Key Concepts**

- **NUMA Nodes**: Logical grouping of CPUs and memory.
- **Local vs. Remote Memory Access**: Accessing memory within the same NUMA node is faster.

#### **Commands and Usage**

**macOS**

- macOS doesn't provide NUMA tools since it uses UMA (Uniform Memory Access).

**Linux**

- **View NUMA Configuration**:

  ```bash
  numactl --hardware
  ```

- **Run Process on Specific NUMA Node**:

  ```bash
  numactl --cpunodebind=0 --membind=0 ./your_application
  ```

- **Check NUMA Policy of a Process**:

  ```bash
  numastat -p $(pidof your_application)
  ```

#### **Practical Exercises**

1. **Inspect NUMA Nodes (Linux Only)**:

   ```bash
   lscpu | grep -i numa
   ```

2. **Run Memory-Intensive Application (Linux Only)**:

   - Install `numactl`:

     ```bash
     sudo apt-get install numactl  # Debian/Ubuntu
     sudo yum install numactl      # CentOS/RHEL
     ```

   - Run application with `numactl`.

3. **Monitor NUMA Statistics (Linux Only)**:

   ```bash
   sudo apt-get install numactl     # Debian/Ubuntu
   sudo yum install numactl numactl-devel numactl-tools  # CentOS/RHEL
   numastat
   ```

#### **Considerations**

- **macOS Users**: Focus on understanding NUMA concepts theoretically.
- **Linux Users**: Hands-on practice with `numactl` and `numastat`.

### **c. Command-Line Efficiency (GNU Readline, Shell Enhancements)**

#### **Overview**

Improving command-line efficiency is essential for productivity. Both macOS and Linux use shells that support command-line editing and history navigation.

#### **Shells**

- **macOS**: Uses `zsh` as the default shell.
- **Linux**: Typically uses `bash`, but `zsh` and others are available.

#### **Common Keyboard Shortcuts**

- **Navigation**:

  - `Ctrl + a`: Move to the beginning of the line.
  - `Ctrl + e`: Move to the end of the line.
  - `Option/Alt + f`: Move forward one word.
  - `Option/Alt + b`: Move backward one word.

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

**macOS (`zsh`)**

- **Edit `.zshrc`**:

  ```bash
  nano ~/.zshrc
  ```

- **Enable Plugins with Oh My Zsh**:

  ```bash
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
  ```

- **Add Plugins**:

  Edit `~/.zshrc`:

  ```bash
  plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
  ```

- **Install Plugins**:

  ```bash
  brew install zsh-autosuggestions zsh-syntax-highlighting
  ```

**Linux (`bash` or `zsh`)**

- **Edit `.bashrc` or `.zshrc`**:

  ```bash
  nano ~/.bashrc  # For bash
  nano ~/.zshrc   # For zsh
  ```

- **Customize Prompt**:

  ```bash
  PS1='\u@\h \w\$ '
  ```

- **Install Oh My Zsh (if using `zsh`)**:

  ```bash
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
  ```

#### **Practical Exercises**

1. **Practice Keyboard Shortcuts**:

   - Spend time using shortcuts instead of arrow keys.

2. **Customize Your Shell Prompt**:

   - Modify `PS1` (for `bash`) or `PROMPT` (for `zsh`).

3. **Enable Syntax Highlighting and Auto-Suggestions**:

   - **macOS**:

     ```bash
     brew install zsh-syntax-highlighting zsh-autosuggestions
     ```

   - **Linux (Debian/Ubuntu)**:

     ```bash
     sudo apt-get install zsh-syntax-highlighting
     ```

     - For `zsh-autosuggestions`, clone the repository:

       ```bash
       git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/zsh-autosuggestions
       ```

       Add to `.zshrc`:

       ```bash
       source ~/.zsh/zsh-autosuggestions/zsh-autosuggestions.zsh
       ```

#### **Considerations**

- **Reload Shell Configuration**:

  ```bash
  source ~/.bashrc    # For bash
  source ~/.zshrc     # For zsh
  ```

- **Portability**: Keep customizations portable across systems.

---

## **3. Git Proficiency**

Git is an essential tool for version control in software development.

### **a. Cloning Repositories with Submodules**

#### **Overview**

Submodules allow you to include other repositories within your repository.

#### **Installation**

- **macOS**:

  ```bash
  git --version  # Should be pre-installed
  ```

- **Linux**:

  - **Debian/Ubuntu**:

    ```bash
    sudo apt-get install git
    ```

  - **CentOS/RHEL**:

    ```bash
    sudo yum install git
    ```

#### **Commands and Usage**

- **Clone with Submodules**:

  ```bash
  git clone --recurse-submodules <repository_url>
  ```

- **Initialize and Update Submodules**:

  ```bash
  git submodule update --init --recursive
  ```

#### **Practical Exercises**

1. **Clone a Repository with Submodules**:

   ```bash
   git clone --recurse-submodules https://github.com/yourusername/repo-with-submodules.git
   ```

2. **Add a Submodule**:

   ```bash
   git submodule add https://github.com/username/submodule-repo.git path/to/submodule
   git commit -am "Add submodule"
   ```

3. **Update Submodules**:

   ```bash
   git submodule update --remote
   ```

#### **Considerations**

- **Commit Changes in Submodules**: Remember to push changes in submodules separately.
- **Beware of Specific Commits**: Submodules point to specific commits.

### **b. Forking and Making Changes**

#### **Overview**

Forking allows you to work on a copy of someone else's repository.

#### **Practical Exercises**

1. **Fork a Repository**:

   - Use GitHub or GitLab interface to fork.

2. **Clone Your Fork**:

   ```bash
   git clone https://github.com/yourusername/forked-repo.git
   ```

3. **Set Upstream Remote**:

   ```bash
   git remote add upstream https://github.com/originalusername/original-repo.git
   ```

4. **Synchronize with Upstream**:

   ```bash
   git fetch upstream
   git merge upstream/main
   ```

5. **Create a Feature Branch**:

   ```bash
   git checkout -b feature/new-feature
   ```

6. **Make Changes and Commit**:

   ```bash
   git add .
   git commit -m "Describe your changes"
   git push origin feature/new-feature
   ```

7. **Create a Pull Request**:

   - Use the web interface to submit a pull request.

#### **Considerations**

- **Regular Updates**: Keep your fork updated.
- **Follow Guidelines**: Adhere to contribution guidelines.

### **c. Merging Back and Updating Upstream Submodules**

#### **Practical Exercises**

1. **Sync Fork with Upstream**:

   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   git push origin main
   ```

2. **Update Submodules After Upstream Changes**:

   ```bash
   git submodule update --init --recursive
   ```

3. **Handle Merge Conflicts**:

   - Use `git mergetool` or resolve conflicts manually.
   - After resolving:

     ```bash
     git add .
     git commit
     ```

#### **Considerations**

- **Communication**: Inform maintainers of submodule updates.
- **Testing**: Ensure updates don't break functionality.

---

## **4. Ansible**

Ansible automates configuration management and deployment tasks.

### **a. Installation**

**macOS**

- **Using Homebrew**:

  ```bash
  brew install ansible
  ```

**Linux**

- **Debian/Ubuntu**:

  ```bash
  sudo apt-get update
  sudo apt-get install ansible
  ```

- **CentOS/RHEL**:

  ```bash
  sudo yum install epel-release
  sudo yum install ansible
  ```

### **b. Configuration Management**

#### **Setting Up Test Environments**

- **macOS and Linux**:

  - **Install VirtualBox and Vagrant**:

    - **macOS**:

      ```bash
      brew install --cask virtualbox vagrant
      ```

    - **Linux (Debian/Ubuntu)**:

      ```bash
      sudo apt-get install virtualbox vagrant
      ```

  - **Create a Vagrantfile**:

    ```bash
    mkdir ansible-test
    cd ansible-test
    vagrant init ubuntu/focal64
    ```

  - **Start the VM**:

    ```bash
    vagrant up
    ```

  - **SSH into the VM**:

    ```bash
    vagrant ssh
    ```

#### **Configuring Ansible Inventory**

- **Create an Inventory File `inventory`**:

  ```ini
  [testservers]
  127.0.0.1 ansible_port=2222 ansible_user=vagrant ansible_private_key_file=.vagrant/machines/default/virtualbox/private_key
  ```

#### **Running Ad-Hoc Commands**

1. **Ping the Server**:

   ```bash
   ansible all -i inventory -m ping
   ```

2. **Install a Package**:

   ```bash
   ansible all -i inventory -m apt -a "name=htop state=present update_cache=yes" --become
   ```

   - **Note**: Replace `apt` with `yum` for CentOS/RHEL VMs.

#### **Writing Playbooks**

1. **Create a Playbook `setup.yml`**:

   ```yaml
   - name: Set up test server
     hosts: testservers
     become: yes
     tasks:
       - name: Update package cache
         apt:
           update_cache: yes
         when: ansible_os_family == 'Debian'

       - name: Install packages
         package:
           name:
             - htop
             - git
           state: present
   ```

2. **Run the Playbook**:

   ```bash
   ansible-playbook -i inventory setup.yml
   ```

#### **Using Roles**

1. **Create a Role**:

   ```bash
   ansible-galaxy init roles/common
   ```

2. **Define Tasks in `roles/common/tasks/main.yml`**:

   ```yaml
   - name: Install common packages
     package:
       name:
         - curl
         - vim
       state: present
   ```

3. **Update Playbook to Use the Role**:

   ```yaml
   - name: Set up test server
     hosts: testservers
     become: yes
     roles:
       - common
   ```

#### **Practical Exercises**

1. **User Management**:

   - Create users, set passwords, and manage SSH keys.

2. **Web Server Configuration**:

   - Install and configure Nginx or Apache.

3. **File Management with Templates**:

   - Use `copy` and `template` modules.

#### **Considerations**

- **Idempotency**: Ensure tasks can run multiple times safely.
- **Variables and Facts**: Use `vars` and `ansible_facts`.

### **c. Advanced Concepts**

#### **Ansible Vault**

1. **Encrypt Variables**:

   ```bash
   ansible-vault create vars/secrets.yml
   ```

2. **Use Encrypted Files**:

   ```yaml
   vars_files:
     - vars/secrets.yml
   ```

3. **Run Playbook with Vault**:

   ```bash
   ansible-playbook -i inventory setup.yml --ask-vault-pass
   ```

#### **Dynamic Inventory**

- Use dynamic inventory scripts for cloud environments (AWS, Azure).

#### **Error Handling**

- **Ignore Errors**:

  ```yaml
  ignore_errors: yes
  ```

- **Handlers and Notifications**:

  ```yaml
  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted
  ```

#### **Practical Exercises**

1. **Encrypt Sensitive Data**.

2. **Conditional Tasks and Loops**.

3. **Roles with Dependencies**.

---

## **5. Terraform**

Terraform is used for infrastructure provisioning.

### **a. Installation**

**macOS**

- **Using Homebrew**:

  ```bash
  brew tap hashicorp/tap
  brew install hashicorp/tap/terraform
  ```

**Linux**

- **Debian/Ubuntu**:

  ```bash
  sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
  curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
  sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
  sudo apt-get update && sudo apt-get install terraform
  ```

- **CentOS/RHEL**:

  ```bash
  sudo yum install -y yum-utils
  sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
  sudo yum -y install terraform
  ```

### **b. AWS Infrastructure Automation**

#### **AWS Setup**

- **Create AWS Account**.

- **Install AWS CLI**:

  **macOS**:

  ```bash
  brew install awscli
  ```

  **Linux**:

  ```bash
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
  ```

- **Configure AWS CLI**:

  ```bash
  aws configure
  ```

#### **Writing Terraform Configurations**

1. **Create Directory and Files**:

   ```bash
   mkdir terraform-aws
   cd terraform-aws
   touch main.tf variables.tf outputs.tf
   ```

2. **Define Provider in `main.tf`**:

   ```hcl
   provider "aws" {
     region = var.region
   }
   ```

3. **Define Resources**:

   ```hcl
   resource "aws_instance" "web" {
     ami           = var.ami
     instance_type = var.instance_type

     tags = {
       Name = "WebServer"
     }
   }
   ```

4. **Define Variables in `variables.tf`**:

   ```hcl
   variable "region" {
     default = "us-east-1"
   }

   variable "ami" {
     default = "ami-0c94855ba95c71c99"
   }

   variable "instance_type" {
     default = "t2.micro"
   }
   ```

5. **Define Outputs in `outputs.tf`**:

   ```hcl
   output "instance_ip" {
     value = aws_instance.web.public_ip
   }
   ```

6. **Initialize and Apply**:

   ```bash
   terraform init
   terraform plan
   terraform apply
   ```

#### **Practical Exercises**

1. **Security Groups**:

   - Create and associate security groups.

2. **Variables and Outputs**:

   - Parameterize configurations.

3. **Remote State Storage**:

   - Configure S3 backend.

#### **Considerations**

- **Cost Management**: Destroy resources when not in use.

### **c. Best Practices**

#### **Modules**

- **Create Reusable Modules**.

- **Organize Code**.

#### **Version Control**

- **Use Git**.

- **Ignore Sensitive Files**:

  Create `.gitignore`:

  ```
  .terraform/
  terraform.tfstate
  terraform.tfstate.backup
  ```

#### **Collaboration**

- **Remote State Locking**.

- **Document Configurations**.

---

## **6. Oracle Cloud and OKE Cluster with Cluster API**

### **a. Oracle Cloud Setup**

#### **Sign Up and Install CLI**

- **Create Account**.

- **Install OCI CLI**:

  **macOS**:

  ```bash
  brew install oci-cli
  ```

  **Linux**:

  ```bash
  bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
  ```

#### **Configure CLI**

```bash
oci setup config
```

### **b. Kubernetes Cluster Management with Cluster API**

#### **Install Prerequisites**

- **kubectl**:

  **macOS**:

  ```bash
  brew install kubectl
  ```

  **Linux**:

  ```bash
  sudo apt-get install -y apt-transport-https
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
  sudo apt-get update
  sudo apt-get install -y kubectl
  ```

- **clusterctl**:

  Download from GitHub releases.

- **kind**:

  **macOS**:

  ```bash
  brew install kind
  ```

  **Linux**:

  ```bash
  curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
  chmod +x ./kind
  sudo mv ./kind /usr/local/bin/kind
  ```

#### **Initialize Cluster API**

1. **Create Management Cluster**:

   ```bash
   kind create cluster
   ```

2. **Initialize Providers**:

   ```bash
   clusterctl init --infrastructure oracle
   ```

### **c. Practical Exercises**

#### **Deploy Workload Cluster**

1. **Generate Cluster Configuration**:

   ```bash
   clusterctl generate cluster my-oke-cluster --infrastructure oracle > my-oke-cluster.yaml
   ```

2. **Customize and Apply**:

   ```bash
   kubectl apply -f my-oke-cluster.yaml
   ```

3. **Monitor Cluster Creation**:

   ```bash
   kubectl get clusters
   ```

#### **Deploy Applications**

1. **Switch Context**:

   ```bash
   export KUBECONFIG=./my-oke-cluster.kubeconfig
   ```

2. **Deploy Nginx**:

   ```bash
   kubectl apply -f https://k8s.io/examples/application/deployment.yaml
   ```

#### **Cleanup**

- **Delete Cluster**:

  ```bash
  kubectl delete cluster my-oke-cluster
  ```

#### **Considerations**

- **Permissions**: Ensure proper OCI permissions.
- **Costs**: Monitor resource usage.

---

## **7. Additional Resources**

- **Linux**

  - _The Linux Command Line_ by William Shotts
  - _Linux Performance and Tuning Guidelines_

- **macOS**

  - _macOS Terminal and Shell_ by Apple

- **Git**

  - _Pro Git_ by Scott Chacon and Ben Straub

- **Ansible**

  - _Ansible for DevOps_ by Jeff Geerling

- **Terraform**

  - _Terraform Up and Running_ by Yevgeniy Brikman

- **Kubernetes**

  - _Kubernetes in Action_ by Marko Lukša

---

## **8. Conclusion**

This comprehensive guide covers both macOS and Linux environments, ensuring you're well-prepared for the hybrid infrastructure at high-speed trading firm. By working through the practical exercises, you'll gain hands-on experience that's directly applicable to your internship.
