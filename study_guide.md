# Comprehensive Study Guide for Your DevOps and System Administrator Internship

---

## Table of Contents

1. [Introduction](#introduction)
2. [System Internals](#system-internals)
   - [1.1. sysctl](#11-sysctl)
   - [1.2. NUMA (Non-Uniform Memory Access)](#12-numa-non-uniform-memory-access)
   - [1.3. Command-Line Efficiency](#13-command-line-efficiency)
3. [Git Proficiency](#git-proficiency)
   - [2.1. Cloning Repositories with Submodules](#21-cloning-repositories-with-submodules)
   - [2.2. Forking and Making Changes](#22-forking-and-making-changes)
   - [2.3. Merging Back and Updating Upstream Submodules](#23-merging-back-and-updating-upstream-submodules)
4. [Ansible](#ansible)
   - [3.1. Configuration Management](#31-configuration-management)
   - [3.2. Advanced Concepts](#32-advanced-concepts)
5. [Terraform](#terraform)
   - [4.1. AWS Infrastructure Automation](#41-aws-infrastructure-automation)
   - [4.2. Best Practices](#42-best-practices)
6. [Oracle Cloud and OKE Cluster with Cluster API](#oracle-cloud-and-oke-cluster-with-cluster-api)
   - [5.1. Oracle Cloud Setup](#51-oracle-cloud-setup)
   - [5.2. Kubernetes Cluster Management with Cluster API](#52-kubernetes-cluster-management-with-cluster-api)
   - [5.3. Practical Exercises](#53-practical-exercises)
7. [Additional Resources](#additional-resources)
8. [Conclusion](#conclusion)

---

## Introduction

Welcome to your comprehensive study guide tailored to prepare you for your upcoming DevOps and System Administrator internship at High-Speed Trading Firm. This guide covers essential topics recommended by your future team, focusing on both macOS and Linux environments to align with the hybrid infrastructure you'll encounter. The guide emphasizes practical, hands-on exercises that mimic the work of a platform engineer at a high-speed trading firm, providing you with real-world scenarios and challenges.

---

## System Internals

Understanding system internals is crucial for optimizing performance, especially in environments that demand high throughput and low latency, such as high-speed trading platforms.

### 1.1. sysctl

#### Overview

`sysctl` is a tool that allows you to view and modify kernel parameters at runtime. These parameters control various aspects of system behavior, including networking, security, and hardware configurations.

#### Key Concepts

- **Kernel Parameters**: Variables that control the operation of the kernel.
- **Persistence**: Changes made via `sysctl` are temporary unless configured to persist across reboots.

#### macOS vs. Linux Differences

- **macOS**: Uses BSD-style `sysctl`. Some parameters may differ from Linux.
- **Linux**: Uses GNU-style `sysctl`.

#### Commands and Usage

**Viewing All Parameters**

- **macOS and Linux**:

  ```bash
  sysctl -a
  ```

  _Description_: Lists all kernel parameters and their current values.

  _Possible Output_:

  ```
  net.ipv4.ip_forward = 0
  net.core.somaxconn = 128
  ...
  ```

**Getting Specific Parameter**

- **macOS**:

  ```bash
  sysctl net.inet.ip.forwarding
  ```

  _Description_: Checks if IP forwarding is enabled.

  _Possible Output_:

  ```
  net.inet.ip.forwarding: 0
  ```

- **Linux**:

  ```bash
  sysctl net.ipv4.ip_forward
  ```

  _Description_: Checks if IP forwarding is enabled.

  _Possible Output_:

  ```
  net.ipv4.ip_forward = 0
  ```

**Setting Parameter Temporarily**

- **macOS**:

  ```bash
  sudo sysctl -w net.inet.ip.forwarding=1
  ```

  _Description_: Enables IP forwarding. Useful for setting up routing or NAT.

  _Possible Output_:

  ```
  net.inet.ip.forwarding: 0 -> 1
  ```

- **Linux**:

  ```bash
  sudo sysctl -w net.ipv4.ip_forward=1
  ```

  _Description_: Enables IP forwarding.

  _Possible Output_:

  ```
  net.ipv4.ip_forward = 1
  ```

**Setting Parameter Permanently**

- **macOS**:

  macOS doesn't support `/etc/sysctl.conf` by default. To make persistent changes, you can create a `plist` file in `/Library/LaunchDaemons/`.

- **Linux**:

  ```bash
  echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.conf
  sudo sysctl -p
  ```

  _Description_: Adds the setting to `/etc/sysctl.conf` and reloads the configuration to make it persistent across reboots.

  _Possible Output_:

  ```
  net.ipv4.ip_forward = 1
  ```

#### Practical Exercises

1. **Optimize Network Performance for Low-Latency Applications**

   _As a platform engineer at a high-speed trading firm, network performance is critical. The following exercises help optimize network settings for low latency._

   - **Adjust TCP Parameters (Linux)**:

     ```bash
     sudo sysctl -w net.core.netdev_max_backlog=5000
     sudo sysctl -w net.core.rmem_max=16777216
     sudo sysctl -w net.core.wmem_max=16777216
     sudo sysctl -w net.ipv4.tcp_rmem="4096 87380 16777216"
     sudo sysctl -w net.ipv4.tcp_wmem="4096 65536 16777216"
     ```

     _Description_: Increases the maximum buffer sizes for sending and receiving data to handle high volumes of network traffic.

     _Possible Outputs_:

     ```
     net.core.netdev_max_backlog = 5000
     net.core.rmem_max = 16777216
     ...
     ```

   - **Make Changes Persistent (Linux)**:

     ```bash
     sudo bash -c 'cat <<EOF >> /etc/sysctl.conf
     net.core.netdev_max_backlog = 5000
     net.core.rmem_max = 16777216
     net.core.wmem_max = 16777216
     net.ipv4.tcp_rmem = 4096 87380 16777216
     net.ipv4.tcp_wmem = 4096 65536 16777216
     EOF'
     sudo sysctl -p
     ```

     _Description_: Adds network optimizations to `/etc/sysctl.conf` and reloads the configuration.

     _Possible Outputs_:

     ```
     net.core.netdev_max_backlog = 5000
     net.core.rmem_max = 16777216
     ...
     ```

2. **Increase File Descriptors Limit**

   _High-speed trading applications may require a large number of open files._

   - **Check Current Limit (Linux and macOS)**:

     ```bash
     ulimit -n
     ```

     _Description_: Displays the current limit of open file descriptors.

     _Possible Output_:

     ```
     1024
     ```

   - **Increase Limit Temporarily (Linux)**:

     ```bash
     sudo sysctl -w fs.file-max=100000
     ulimit -n 100000
     ```

     _Description_: Increases the maximum number of open file descriptors.

     _Possible Outputs_:

     ```
     fs.file-max = 100000
     ```

   - **Increase Limit Temporarily (macOS)**:

     ```bash
     sudo launchctl limit maxfiles 100000 100000
     ulimit -n 100000
     ```

     _Description_: Sets the soft and hard limits for open files.

3. **Tune Swappiness (Linux)**

   _Minimize swapping to disk to reduce latency._

   ```bash
   sudo sysctl -w vm.swappiness=10
   ```

   _Description_: Reduces the kernel's tendency to swap processes out of physical memory.

   _Possible Output_:

   ```
   vm.swappiness = 10
   ```

#### Considerations

- **Safety**: Always understand the impact of kernel parameter changes. Incorrect settings can degrade system performance or stability.
- **Testing**: Apply changes in a test environment before deploying to production.
- **Documentation**: Keep a record of changes for maintenance and troubleshooting.

### 1.2. NUMA (Non-Uniform Memory Access)

#### Overview

NUMA is a memory design used in multiprocessing where memory access time depends on the memory location relative to the processor. Optimizing NUMA settings can significantly improve performance for applications that require low latency and high throughput.

#### Key Concepts

- **NUMA Nodes**: Logical groupings of processors and memory.
- **Local vs. Remote Memory Access**: Accessing memory within the same NUMA node is faster than accessing memory on a different node.

#### Commands and Usage (Linux Only)

**Viewing NUMA Configuration**

```bash
numactl --hardware
```

_Description_: Displays the NUMA topology of the system.

_Possible Output_:

```
available: 2 nodes (0-1)
node 0 cpus: 0-7
node 0 size: 16384 MB
node 0 free: 8000 MB
node 1 cpus: 8-15
node 1 size: 16384 MB
node 1 free: 7500 MB
```

**Running a Process on a Specific NUMA Node**

```bash
numactl --cpunodebind=0 --membind=0 ./your_application
```

_Description_: Binds your application to run on CPUs and memory of NUMA node 0.

**Checking NUMA Policy of a Process**

```bash
numastat -p $(pidof your_application)
```

_Description_: Displays NUMA statistics for a specific process.

#### Practical Exercises

1. **Optimize Application Performance with NUMA**

   _In high-speed trading, it's essential to minimize latency. Binding critical applications to specific NUMA nodes can reduce memory access times._

   - **Install `numactl` and `numastat` (if not installed)**:

     ```bash
     sudo apt-get install numactl numactl-devel numastat  # Debian/Ubuntu
     sudo yum install numactl numactl-devel numastat      # CentOS/RHEL
     ```

   - **Identify NUMA Nodes and CPUs**:

     ```bash
     lscpu | grep 'NUMA node(s)'
     lscpu | grep 'NUMA node0 CPU(s)'
     lscpu | grep 'NUMA node1 CPU(s)'
     ```

     _Description_: Shows the number of NUMA nodes and which CPUs are assigned to each node.

     _Possible Output_:

     ```
     NUMA node(s):        2
     NUMA node0 CPU(s):   0-7
     NUMA node1 CPU(s):   8-15
     ```

   - **Run a Latency-Sensitive Application on NUMA Node 0**:

     ```bash
     numactl --cpunodebind=0 --membind=0 ./latency_sensitive_app
     ```

     _Description_: Ensures the application runs on CPUs and memory local to NUMA node 0.

2. **Analyze NUMA Memory Usage**

   - **Monitor System-Wide NUMA Statistics**:

     ```bash
     numastat
     ```

     _Description_: Provides an overview of NUMA memory allocation and usage.

     _Possible Output_:

     ```
     node0 node1
     numa_hit        5000000 4000000
     numa_miss          1000    2000
     numa_foreign       1000    2000
     ...
     ```

   - **Monitor NUMA Statistics for a Specific Process**:

     ```bash
     numastat -p $(pidof latency_sensitive_app)
     ```

     _Description_: Shows how the application's memory accesses are distributed across NUMA nodes.

     _Possible Output_:

     ```
     Per-node process memory usage (in MBs)
     PID        Node 0       Node 1
     12345        500          100
     ```

#### Considerations

- **Application Compatibility**: Ensure the application supports NUMA optimizations.
- **Testing**: Benchmark performance before and after applying NUMA settings.
- **System Architecture**: NUMA is relevant on multi-socket systems; single-socket systems may not benefit.

### 1.3. Command-Line Efficiency

#### Overview

Enhancing command-line efficiency can significantly improve productivity. Mastering shell features and shortcuts is essential for a platform engineer.

#### Key Concepts

- **Shells**: Command-line interpreters like `bash` and `zsh`.
- **Command-Line Editing**: Using keyboard shortcuts to navigate and edit commands.
- **History Navigation**: Accessing and reusing previous commands.

#### Common Keyboard Shortcuts

- **Navigation**:
  - `Ctrl + a`: Move cursor to the beginning of the line.
  - `Ctrl + e`: Move cursor to the end of the line.
  - `Alt + f` / `Option + f`: Move forward one word.
  - `Alt + b` / `Option + b`: Move backward one word.
- **Editing**:
  - `Ctrl + k`: Delete from cursor to end of line.
  - `Ctrl + u`: Delete from cursor to beginning of line.
  - `Ctrl + w`: Delete word before cursor.
  - `Ctrl + y`: Yank (paste) the last deleted text.
- **History**:
  - `Ctrl + r`: Reverse search command history.
  - `Ctrl + p`: Previous command.
  - `Ctrl + n`: Next command.

#### Customization

**For `bash` and `zsh`**

- **Customize Prompt**:

  - Edit `.bashrc` or `.zshrc`:

    ```bash
    PS1='\u@\h:\w\$ '
    ```

    _Description_: Sets the prompt to display username, hostname, and current working directory.

- **Enable Auto-Completion**:

  - **bash**:

    ```bash
    sudo apt-get install bash-completion  # Debian/Ubuntu
    sudo yum install bash-completion      # CentOS/RHEL
    ```

    Add to `.bashrc`:

    ```bash
    if [ -f /etc/bash_completion ]; then
        . /etc/bash_completion
    fi
    ```

- **Install `zsh` and Oh My Zsh**:

  - **Install `zsh`**:

    ```bash
    sudo apt-get install zsh     # Debian/Ubuntu
    sudo yum install zsh         # CentOS/RHEL
    ```

  - **Install Oh My Zsh**:

    ```bash
    sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
    ```

#### Practical Exercises

1. **Automate Frequent Commands with Aliases**

   - **Add Aliases to `.bashrc` or `.zshrc`**:

     ```bash
     alias ll='ls -la'
     alias gs='git status'
     alias ga='git add .'
     alias gp='git push'
     ```

     _Description_: Creates shortcuts for frequently used commands.

   - **Reload Shell Configuration**:

     ```bash
     source ~/.bashrc    # For bash
     source ~/.zshrc     # For zsh
     ```

2. **Enhance Shell with Plugins**

   - **For `zsh` with Oh My Zsh**:

     - **Enable Plugins**:

       Edit `~/.zshrc`:

       ```bash
       plugins=(git docker kubectl)
       ```

     - **Install and Enable `zsh-autosuggestions`**:

       ```bash
       git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
       ```

       Add `zsh-autosuggestions` to plugins in `~/.zshrc`:

       ```bash
       plugins=(git docker kubectl zsh-autosuggestions)
       ```

   - **For `bash`**:

     - **Install `bash-git-prompt`**:

       ```bash
       git clone https://github.com/magicmonty/bash-git-prompt.git ~/.bash-git-prompt --depth=1
       ```

       Add to `~/.bashrc`:

       ```bash
       GIT_PROMPT_ONLY_IN_REPO=1
       source ~/.bash-git-prompt/gitprompt.sh
       ```

3. **Use `tmux` for Session Management**

   - **Install `tmux`**:

     ```bash
     sudo apt-get install tmux  # Debian/Ubuntu
     sudo yum install tmux      # CentOS/RHEL
     ```

   - **Start a New Session**:

     ```bash
     tmux new -s dev_session
     ```

     _Description_: Creates a new tmux session named `dev_session`.

   - **Split Panes and Windows**:

     - **Split Horizontally**: `Ctrl + b` then `%`
     - **Split Vertically**: `Ctrl + b` then `"`

   - **Detach and Reattach Sessions**:

     - **Detach**: `Ctrl + b` then `d`
     - **Reattach**:

       ```bash
       tmux attach -t dev_session
       ```

   _Description_: `tmux` allows you to maintain persistent sessions, which is useful when managing long-running tasks or remote servers.

#### Considerations

- **Practice**: Regular use of shortcuts and tools will improve proficiency.
- **Customization**: Keep configurations portable using dotfiles repositories.

---

## Git Proficiency

Mastering Git workflows is essential for collaboration and maintaining code integrity in a fast-paced environment.

### 2.1. Cloning Repositories with Submodules

#### Overview

Submodules allow you to include external repositories within your project, enabling modular development and reuse of code.

#### Commands and Usage

**Cloning a Repository with Submodules**

```bash
git clone --recurse-submodules <repository_url>
```

_Description_: Clones the repository and initializes and updates all its submodules.

_Possible Output_:

```
Cloning into 'repository'...
Submodule 'submodule_path' (https://github.com/username/submodule.git) registered for path 'submodule_path'
Cloning into 'submodule_path'...
Submodule path 'submodule_path': checked out 'commit_hash'
```

**Initializing and Updating Submodules After Cloning**

```bash
git submodule update --init --recursive
```

_Description_: Initializes and updates submodules in case they weren't cloned with `--recurse-submodules`.

#### Practical Exercises

1. **Set Up a Project with Submodules**

   _In a high-speed trading environment, you might work with codebases that rely on external libraries or modules._

   - **Create a New Repository**:

     ```bash
     mkdir trading-platform
     cd trading-platform
     git init
     ```

   - **Add a Submodule**:

     ```bash
     git submodule add https://github.com/username/market-data-module.git modules/market-data
     ```

     _Description_: Adds the `market-data-module` repository as a submodule under `modules/market-data`.

     _Possible Output_:

     ```
     Cloning into 'modules/market-data'...
     ```

   - **Commit Changes**:

     ```bash
     git commit -am "Add market data module as submodule"
     ```

   - **Clone the Repository Elsewhere**:

     ```bash
     git clone --recurse-submodules <repository_url>
     ```

     _Description_: Ensures the submodules are cloned along with the main repository.

2. **Update Submodules to Point to Latest Commit**

   - **Navigate to Submodule Directory**:

     ```bash
     cd modules/market-data
     ```

   - **Check for Updates**:

     ```bash
     git fetch
     git checkout origin/main
     ```

   - **Commit the Updated Submodule Reference**:

     ```bash
     cd ../../
     git add modules/market-data
     git commit -m "Update market data module to latest version"
     ```

   - **Push Changes**:

     ```bash
     git push origin main
     ```

   _Description_: Updates the submodule to the latest commit and records the change in the main repository.

#### Considerations

- **Submodule Pitfalls**: Submodules can introduce complexity. Always ensure that team members are aware of submodule updates.
- **Consistency**: Use the same commands (`--recurse-submodules`) when cloning and updating to prevent discrepancies.

### 2.2. Forking and Making Changes

#### Overview

Forking a repository allows you to contribute to projects without affecting the original codebase. It's a common workflow in collaborative environments.

#### Practical Exercises

1. **Fork a Repository**

   - **On GitHub/GitLab**:

     - Navigate to the repository you want to fork.
     - Click on the "Fork" button.

2. **Clone Your Fork Locally**

   ```bash
   git clone https://github.com/yourusername/forked-repo.git
   cd forked-repo
   ```

   _Description_: Clones your forked repository to your local machine.

3. **Set Upstream Remote**

   ```bash
   git remote add upstream https://github.com/originalowner/original-repo.git
   ```

   _Description_: Adds the original repository as an upstream remote to fetch updates.

4. **Synchronize Your Fork**

   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main
   git push origin main
   ```

   _Description_: Updates your fork with changes from the original repository.

5. **Create a Feature Branch**

   ```bash
   git checkout -b feature/improve-latency
   ```

   _Description_: Creates a new branch for your feature.

6. **Make Changes and Commit**

   - **Edit Files**:

     - Make code changes to improve application latency.

   - **Stage and Commit Changes**:

     ```bash
     git add .
     git commit -m "Optimize data processing for lower latency"
     ```

   _Description_: Records your changes in the local repository.

7. **Push Changes to Your Fork**

   ```bash
   git push origin feature/improve-latency
   ```

   _Description_: Pushes your feature branch to your fork on GitHub/GitLab.

8. **Create a Pull Request**

   - Navigate to your forked repository online.
   - Click on "Compare & pull request".
   - Provide a clear description of your changes and submit the pull request.

   _Description_: Initiates the process to merge your changes into the original repository.

#### Considerations

- **Code Reviews**: Be prepared for feedback and make necessary revisions.
- **Branch Naming**: Use descriptive names for branches to reflect the work being done.
- **Commit Messages**: Write clear and concise messages.

### 2.3. Merging Back and Updating Upstream Submodules

#### Practical Exercises

1. **Handle Merge Conflicts**

   - **Fetch Latest Changes from Upstream**

     ```bash
     git fetch upstream
     ```

   - **Merge Upstream Changes**

     ```bash
     git merge upstream/main
     ```

     _Possible Output_:

     ```
     Auto-merging file.txt
     CONFLICT (content): Merge conflict in file.txt
     Automatic merge failed; fix conflicts and then commit the result.
     ```

   - **Resolve Conflicts**

     - Open the conflicting files and manually resolve conflicts.
     - Mark conflicts as resolved:

       ```bash
       git add file.txt
       ```

   - **Commit Merge**

     ```bash
     git commit -m "Merge upstream changes into feature branch"
     ```

2. **Update Submodules After Upstream Changes**

   - **Update Submodules**

     ```bash
     git submodule update --remote --merge
     ```

     _Description_: Fetches and merges changes from the submodule's default branch.

   - **Commit Updated Submodule**

     ```bash
     git add path/to/submodule
     git commit -m "Update submodule to latest commit"
     ```

   - **Push Changes**

     ```bash
     git push origin feature/improve-latency
     ```

3. **Finalize Pull Request**

   - **Address Feedback**

     - Incorporate any requested changes from code reviewers.

   - **Merge Pull Request**

     - Once approved, merge the pull request into the main branch.

#### Considerations

- **Continuous Integration**: Ensure that automated tests pass after merging changes.
- **Submodule Updates**: Communicate with the team when submodules are updated to prevent unexpected issues.

---

## Ansible

Automating configuration management with Ansible ensures consistency and efficiency in deploying infrastructure.

**Linux**

- **Debian/Ubuntu**:

  ```bash
  sudo apt-get update
  sudo apt-get install ansible
  ```

_Description_: Installs Ansible using the package manager.

### 3.1. Configuration Management

#### Setting Up a Test Environment

1. **Create Virtual Machines**

   - **Using Vagrant**:

     - **Install Vagrant and VirtualBox** (if not already installed).

       **macOS**:

       ```bash
       brew install --cask virtualbox vagrant
       ```

       **Linux**:

       ```bash
       sudo apt-get install virtualbox vagrant  # Debian/Ubuntu
       ```

     - **Initialize Vagrant**

       ```bash
       mkdir ansible_lab
       cd ansible_lab
       vagrant init ubuntu/focal64
       ```

     - **Configure `Vagrantfile`**

       Edit `Vagrantfile` to set up multiple VMs:

       ```ruby
       Vagrant.configure("2") do |config|
         config.vm.define "web" do |web|
           web.vm.box = "ubuntu/focal64"
           web.vm.hostname = "web"
           web.vm.network "private_network", ip: "192.168.33.10"
         end

         config.vm.define "db" do |db|
           db.vm.box = "ubuntu/focal64"
           db.vm.hostname = "db"
           db.vm.network "private_network", ip: "192.168.33.11"
         end
       end
       ```

     - **Start VMs**

       ```bash
       vagrant up
       ```

     _Description_: Creates and starts two VMs named `web` and `db`.

2. **Configure Ansible Inventory**

   - **Create an Inventory File `inventory`**

     ```ini
     [webservers]
     192.168.33.10 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/web/virtualbox/private_key

     [dbservers]
     192.168.33.11 ansible_user=vagrant ansible_ssh_private_key_file=.vagrant/machines/db/virtualbox/private_key
     ```

   _Description_: Defines the hosts and variables for Ansible.

#### Writing Playbooks

1. **Create a Playbook `site.yml`**

   ```yaml
   - name: Configure web and db servers
     hosts: all
     become: yes

     vars:
       - http_port: 80

     tasks:
       - name: Ensure latest apt cache update
         apt:
           update_cache: yes
         when: ansible_os_family == 'Debian'

       - name: Install required packages
         apt:
           name: "{{ packages }}"
           state: present
         vars:
           packages:
             - nginx
             - mysql-server
         when: ansible_os_family == 'Debian'

       - name: Start and enable services
         service:
           name: "{{ item }}"
           state: started
           enabled: yes
         loop:
           - nginx
           - mysql
   ```

   _Description_: Configures both web and database servers by installing packages and ensuring services are running.

2. **Run the Playbook**

   ```bash
   ansible-playbook -i inventory site.yml
   ```

   _Description_: Executes the playbook against the hosts defined in the inventory.

3. **Verify Deployment**

   - **On Web Server**

     ```bash
     curl http://192.168.33.10
     ```

     _Description_: Checks if Nginx is serving the default page.

   - **On Database Server**

     ```bash
     mysql -u root -e "SHOW DATABASES;"
     ```

     _Description_: Ensures MySQL is running and accessible.

#### Automating Application Deployment

1. **Create an Ansible Role for the Web Application**

   - **Create Role Structure**

     ```bash
     ansible-galaxy init roles/webapp
     ```

   - **Define Tasks in `roles/webapp/tasks/main.yml`**

     ```yaml
     - name: Deploy web application files
       copy:
         src: app/
         dest: /var/www/html/
         owner: www-data
         group: www-data
         mode: "0755"

     - name: Configure Nginx site
       template:
         src: templates/nginx.conf.j2
         dest: /etc/nginx/sites-available/default

     - name: Restart Nginx
       service:
         name: nginx
         state: restarted
     ```

   ```

   *Description*: Deploys application files, configures Nginx, and restarts the service.

   ```

2. **Add Role to Playbook**

   ```yaml
   - name: Configure web servers
     hosts: webservers
     become: yes
     roles:
       - webapp
   ```

3. **Run the Playbook**

   ```bash
   ansible-playbook -i inventory site.yml
   ```

4. **Verify Application Deployment**

   - **Access the Web Application**

     ```bash
     curl http://192.168.33.10
     ```

     _Description_: Should display your web application's content.

#### Considerations

- **Idempotency**: Ensure playbooks can be run multiple times without causing unintended side effects.
- **Variables and Templates**: Use variables and Jinja2 templates to manage configuration files dynamically.
- **Testing**: Use Ansible's `--check` mode to perform dry runs.

### 3.2. Advanced Concepts

#### Ansible Vault

1. **Encrypt Sensitive Data**

   - **Create Encrypted Variable File**

     ```bash
     ansible-vault create vars/secret.yml
     ```

     _Enter variables like:_

     ```yaml
     db_password: "SecurePassword123"
     ```

2. **Use Encrypted Variables in Playbook**

   - **Include Encrypted Variables**

     ```yaml
     vars_files:
       - vars/secret.yml
     ```

   - **Use Variables**

     ```yaml
     - name: Configure database
       mysql_user:
         name: app_user
         password: "{{ db_password }}"
         priv: "*.*:ALL"
     ```

3. **Run Playbook with Vault Password**

   ```bash
   ansible-playbook -i inventory site.yml --ask-vault-pass
   ```

#### Dynamic Inventory

1. **Use AWS Dynamic Inventory**

   - **Install `boto3` and `botocore`**

     ```bash
     pip install boto boto3 botocore
     ```

   - **Configure AWS Credentials**

     - Set up `~/.aws/credentials` and `~/.aws/config`.

   - **Download AWS Inventory Script**

     ```bash
     wget https://raw.githubusercontent.com/ansible/ansible/stable-2.9/contrib/inventory/ec2.py
     chmod +x ec2.py
     ```

   - **Use Dynamic Inventory**

     ```bash
     ansible -i ec2.py all -m ping
     ```

#### Error Handling and Debugging

1. **Use the `debug` Module**

   ```yaml
   - name: Display variable values
     debug:
       var: some_variable
   ```

2. **Handle Task Failures**

   ```yaml
   - name: Attempt to start service
     service:
       name: some_service
       state: started
     ignore_errors: yes
   ```

3. **Use Tags for Selective Runs**

   ```yaml
   - name: Install web packages
     apt:
       name: nginx
       state: present
     tags:
       - web
   ```

   - **Run Playbook with Tags**

     ```bash
     ansible-playbook -i inventory site.yml --tags "web"
     ```

#### Considerations

- **Security**: Protect vault passwords and sensitive data.
- **Scalability**: Use dynamic inventory for environments that change frequently.
- **Modularity**: Organize playbooks and roles for reusability.

---

## Terraform

Automating infrastructure provisioning with Terraform ensures consistency and reduces manual errors.

**Linux**

- **Debian/Ubuntu**:

  ```bash
  sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
  curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
  sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
  sudo apt-get update && sudo apt-get install terraform
  ```

### 4.1. AWS Infrastructure Automation

#### Setting Up AWS Credentials

1. **Install AWS CLI**

   **macOS**

   ```bash
   brew install awscli
   ```

   **Linux**

   ```bash
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   unzip awscliv2.zip
   sudo ./aws/install
   ```

2. **Configure AWS CLI**

   ```bash
   aws configure
   ```

   _Description_: Enters your AWS Access Key, Secret Access Key, and default region.

#### Writing Terraform Configurations

1. **Create Project Directory**

   ```bash
   mkdir tf_highspeed_trading
   cd tf_highspeed_trading
   ```

2. **Define Provider in `main.tf`**

   ```hcl
   provider "aws" {
     region = "us-east-1"
   }
   ```

3. **Create VPC Module**

   - **Create `modules/vpc/main.tf`**

     ```hcl
     resource "aws_vpc" "main" {
       cidr_block = "10.0.0.0/16"
     }

     resource "aws_subnet" "public" {
       vpc_id            = aws_vpc.main.id
       cidr_block        = "10.0.1.0/24"
       availability_zone = "us-east-1a"
     }
     ```

4. **Define Application Infrastructure**

   - **In `main.tf`**

     ```hcl
     module "vpc" {
       source = "./modules/vpc"
     }

     resource "aws_instance" "trading_app" {
       ami           = "ami-0c94855ba95c71c99"  # Amazon Linux 2
       instance_type = "c5.large"
       subnet_id     = module.vpc.public_subnet_id
       user_data     = file("user_data.sh")

       tags = {
         Name = "TradingAppServer"
       }
     }
     ```

   - **Create `user_data.sh`**

     ```bash
     #!/bin/bash
     # Install necessary packages and configure the trading application
     yum update -y
     yum install -y git
     # Clone and run the trading application
     git clone https://github.com/yourcompany/trading-app.git /opt/trading-app
     cd /opt/trading-app
     ./setup.sh
     ```

     _Description_: Bootstraps the instance to run the trading application.

5. **Initialize and Apply Configuration**

   ```bash
   terraform init
   terraform plan
   terraform apply
   ```

   _Description_: Provisions the infrastructure on AWS.

6. **Verify Deployment**

   - **SSH into the Instance**

     ```bash
     ssh -i your_key.pem ec2-user@<public_ip>
     ```

   - **Check Application Status**

     ```bash
     ps aux | grep trading_app
     ```

     _Description_: Ensures the trading application is running.

#### Considerations

- **Performance-Optimized Instances**: Use instance types suitable for high-performance computing (e.g., `c5`, `c5n`).
- **Network Security**: Configure security groups to allow necessary traffic and secure the application.
- **Scalability**: Plan for scaling the infrastructure as needed.

### 4.2. Best Practices

#### Use of Modules

- **Create Reusable Modules**: For VPCs, security groups, instances.

- **Example Module Structure**

  ```
  modules/
    vpc/
      main.tf
      outputs.tf
      variables.tf
    ec2/
      main.tf
      outputs.tf
      variables.tf
  ```

#### Remote State Management

- **Use S3 for Remote State**

  ```hcl
  terraform {
    backend "s3" {
      bucket = "dvtrading-terraform-state"
      key    = "env:/${terraform.workspace}/terraform.tfstate"
      region = "us-east-1"
    }
  }
  ```

  _Description_: Stores the state file in S3 for collaboration.

#### Locking

- **Enable State Locking with DynamoDB**

  ```hcl
  backend "s3" {
    bucket         = "dvtrading-terraform-state"
    key            = "env:/${terraform.workspace}/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
  }
  ```

  _Description_: Prevents concurrent modifications to the state file.

#### Version Control and Collaboration

- **Use Git for Code Management**
- **Implement Code Reviews**: Ensure configurations are peer-reviewed before deployment.

---

## Oracle Cloud and OKE Cluster with Cluster API

### 5.1. Oracle Cloud Setup

#### Sign Up and Install CLI

1. **Create an Oracle Cloud Account**

   - Sign up for a free tier at [Oracle Cloud Free Tier](https://www.oracle.com/cloud/free/).

2. **Install OCI CLI**

   **macOS**

   ```bash
   brew install oci-cli
   ```

   **Linux**

   ```bash
   bash -c "$(curl -L https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.sh)"
   ```

3. **Configure OCI CLI**

   ```bash
   oci setup config
   ```

   _Description_: Configures the CLI with your Oracle Cloud credentials.

### 5.2. Kubernetes Cluster Management with Cluster API

#### Install Prerequisites

- **kubectl**

  **macOS**

  ```bash
  brew install kubectl
  ```

  **Linux**

  ```bash
  curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
  chmod +x ./kubectl
  sudo mv ./kubectl /usr/local/bin/kubectl
  ```

- **clusterctl**

  ```bash
  curl -L https://github.com/kubernetes-sigs/cluster-api/releases/download/v1.0.0/clusterctl-$(uname | tr '[:upper:]' '[:lower:]')-amd64 -o clusterctl
  chmod +x clusterctl
  sudo mv clusterctl /usr/local/bin/
  ```

#### Initialize Cluster API

1. **Create a Kind Cluster**

   ```bash
   kind create cluster
   ```

   _Description_: Sets up a local Kubernetes cluster for management.

2. **Initialize Providers**

   ```bash
   clusterctl init --infrastructure oci
   ```

   _Description_: Initializes the Cluster API with the Oracle Cloud Infrastructure provider.

### 5.3. Practical Exercises

1. **Deploy a Kubernetes Cluster on Oracle Cloud**

   - **Generate Cluster Configuration**

     ```bash
     clusterctl generate cluster my-oke-cluster --infrastructure oci > my-oke-cluster.yaml
     ```

   - **Customize Configuration**

     - Edit `my-oke-cluster.yaml` to set parameters like `nodeShape`, `nodeOCID`, and `compartmentID`.

   - **Apply Configuration**

     ```bash
     kubectl apply -f my-oke-cluster.yaml
     ```

     _Description_: Provisions a Kubernetes cluster on Oracle Cloud.

   - **Monitor Cluster Creation**

     ```bash
     kubectl get clusters
     kubectl get machines
     ```

     _Description_: Checks the status of the cluster and nodes.

2. **Deploy a High-Performance Application**

   - **Create Deployment YAML**

     ```yaml
     apiVersion: apps/v1
     kind: Deployment
     metadata:
       name: hft-app
     spec:
       replicas: 3
       selector:
         matchLabels:
           app: hft-app
       template:
         metadata:
           labels:
             app: hft-app
         spec:
           containers:
             - name: hft-app
               image: yourregistry/hft-app:latest
               resources:
                 limits:
                   cpu: "2"
                   memory: "4Gi"
               env:
                 - name: APP_ENV
                   value: "production"
     ```

     _Description_: Deploys a high-frequency trading application with resource limits.

   - **Apply Deployment**

     ```bash
     kubectl apply -f hft-app-deployment.yaml
     ```

   - **Verify Deployment**

     ```bash
     kubectl get pods -l app=hft-app
     kubectl describe pod <pod_name>
     ```

     _Description_: Checks that pods are running and gathers detailed information.

3. **Implement Node Affinity for Low Latency**

   - **Update Deployment with Node Affinity**

     ```yaml
     spec:
       affinity:
         nodeAffinity:
           requiredDuringSchedulingIgnoredDuringExecution:
             nodeSelectorTerms:
               - matchExpressions:
                   - key: low-latency
                     operator: In
                     values:
                       - "true"
       containers:
         - name: hft-app
           image: yourregistry/hft-app:latest
     ```

     _Description_: Ensures pods are scheduled on nodes labeled for low-latency workloads.

   - **Label Nodes**

     ```bash
     kubectl label nodes <node_name> low-latency=true
     ```

     _Description_: Labels nodes to match the affinity rules.

#### Considerations

- **Resource Optimization**: Use node pools with high-performance shapes.
- **Networking**: Configure network policies and load balancers as needed.
- **Scaling**: Implement Horizontal Pod Autoscalers to adjust replicas based on load.

---

## Additional Resources

- **Linux**

  - _Linux Performance Tuning and Capacity Planning_ by Jason R. Fink
  - _High Performance Linux Clusters with OSCAR, Rocks, OpenMosix, and MPI_ by Joseph Sloan

- **macOS**

  - _macOS Internals: A Systems Approach_ by Jonathan Levin

- **Git**

  - _GitLab Flow_ Documentation: [GitLab Flow](https://docs.gitlab.com/ee/topics/gitlab_flow.html)

- **Ansible**

  - _Ansible for Kubernetes_ by Jeff Geerling

- **Terraform**

  - _Terraform Cookbook_ by Mikael Krief

- **Kubernetes**

  - _Production Kubernetes_ by Josh Rosso, Rich Lander, Alex Brand, Keith Tenzer

- **Oracle Cloud**

  - _Oracle Cloud Infrastructure Documentation_: [OCI Docs](https://docs.oracle.com/en-us/iaas/Content/home.htm)

---

## Conclusion

This comprehensive study guide is designed to equip you with the knowledge and practical skills necessary for your role as a platform engineer at High-Speed Trading Firm. By working through these exercises, you'll gain hands-on experience in optimizing systems for high performance, automating infrastructure, and managing complex deployments—skills that are critical in a high-speed trading environment.

---
