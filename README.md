![](screenshots/banner.png)

> **Owner** : 🧛🏻‍♂️ - Mr. Sup3rN0va | 06-February-2021

> **Tags** : #ssh (💻), #pentesting (👨🏼‍💻), #tools (⚒), #cheatsheet (📜)

---

<h2><b>Table Of Contents</b></h2>

- [**Lazy SSH**](#lazy-ssh)
  - [**SSH with Config**](#ssh-with-config)
  - [**Local, Remote and Dynamic Port Forwarding**](#local-remote-and-dynamic-port-forwarding)
  - [**Mount Remote Drives using SSHFS**](#mount-remote-drives-using-sshfs)
  - [**Sample Config File**](#sample-config-file)

---

## **Lazy SSH**

---

- Most of us knows about SSH - **Secure Shell**, so this post will be for how to be lazy and use `SSH` efficiently
- Feature set :
  - **Quick SSH with config file**
  - **Local, Remote and Dynamic Port Forwarding**
  - **Mount remote drives using SSHFS**

---

<div class='page'/>
☝️ <a href="#">Back to top</a> ☝️

---

### **SSH with Config**

---

- Pre-requisites

  ```sh
  sudo apt-get update; sudo apt-get upgrade -y; sudo apt-get install build-essential linux-headers-`uname -r` -y
  sudo apt-get install sshfs -y
  pip3 install -U pip paramiko --user --no-warn-script-location
  ```

- Steps:
  - Create crypto-keys : `ssh-keygen -t rsa`
  - This will generate crypto-keys and save it in `$HOME/.ssh/` folder
  - Now transfer your `public-key` on to the remote server so that you can do `SSH` without password
  - For that : `ssh-copy-id -i $HOME/.ssh/id_rsa.pub user@10.0.0.1`
  - Now create the `config` file on your machine in `$HOME/.ssh` folder as

  ```txt
  Host b0x
    Hostname 10.0.0.1
    User user
    Port 22
    Compression yes
    IdentityFile ~/.ssh/id_rsa
    ForwardX11 yes
    Protocol 2
    StrictHostKeyChecking no
  ```

  - Change the permissions of the file : `chmod 600 $HOME/.ssh/config`

- Now you can do `ssh b0x` and you are in without password : **EASY EASY** 😋

---

<div class='page'/>
☝️ <a href="#">Back to top</a> ☝️

---

### **Local, Remote and Dynamic Port Forwarding**

---

- This is required when you have to access some services which are by default running on local ports to which you don't have access to on a VM
- Port forwarding helps you to access not reachable services
- Open the `config` created above and add these lines

  ```txt
  Host b0x
    LocalForward 31337 127.0.0.1:31337
    RemoteForward 8000 127.0.0.1:8000
  ```

- Here the format is
  - **Local Port Forwarding** : `<LocalForward> <LocalIP>:<LocalPort> <RemoteIP>:<RemotePort>`
  - **RemoteForward** : `<RemoteForward> <RemoteIP>:<RemotePort> <LocalIP>:<LocalPort>`

- Here if you see entry starts with port because by default, it takes IP as `127.0.0.1`
- Now to only port-forward : `ssh -f -N b0x`
- If you want to `SSH` as well as port-forward both, then `ssh b0x`
- If you want to do this directly then : `ssh -f -N -L 31337:127.0.0.1:31337 <SSH_Server>`
  - `-f` : tells to background `SSH`
  - `-N` : tells not to execute remote command. Only used at the time of port-forwarding
  - `-L` : tells that we are trying local port forwarding
- For **Remote Port Forwarding** : `ssh -f -N -R <REMOTE_IP>:<REMOTE_PORT>:<LOCAL_IP>:<LOCAL_PORT> <SSH_SERVER>`
- For **Dynamic Port Forwarding** : `ssh -D 8123 -f -C -q -N via_host`
  - This is also called as **SOCKS Proxy**
  - `-D` : tells that you are trying Dynamic Port Forwarding
  - `-f` : tells `SSH` to go to background
  - `-C` : tells `SSH` to compress data before sending
  - `-q` : quiet mode enabled
  - `-N` : tells not to execute remote command. Only used at the time of port-forwarding
- To add **Dynamic Port Forwarding** in `config` file, add this line : `DynamicForward 8080`
- **Reference**:
  - [**SSH Port Forwarding**](https://www.ssh.com/ssh/tunneling/example)
  - [**SSH Port Forwarding - Zaiste**](https://zaiste.net/posts/ssh-port-forwarding/)

---

<div class='page'/>
☝️ <a href="#">Back to top</a> ☝️

---

</div>

### **Mount Remote Drives using SSHFS**

---

- This is one of the best features I came across which assists us in mounting remote drive via SSH tunnel
- From pre-requisites, we have already installed `paramiko` and `sshfs`
- I have created scripts which will help us mounting remote drives via `SSH`
- `SSH-Mount` : [**SSH-Mount**](scripts/ssh-mount)
- `SSH-Umount` : [**SSH-Umount**](scripts/ssh-umount)
- **Reference**:
  - [**SSH File Transfers**](https://help.ubuntu.com/community/SSH/TransferFiles)
  - [**SSHFS**](https://help.ubuntu.com/community/SSHFS)

> **NOTE**: Both the mount points are in `$HOME` directories

---

<div class='page'/>
☝️ <a href="#">Back to top</a> ☝️

---

</div>

### **Sample Config File**

  ```txt
  Host b0x
    Hostname 10.0.0.1
    User user
    Port 22
    Compression yes
    IdentityFile $HOME/.ssh/id_rsa
    ForwardX11 yes
    Protocol 2
    StrictHostKeyChecking no
    LocalForward 31337 127.0.0.1:31337 # ---- Only if necessary
    RemoteForward 8000 127.0.0.1:8000 # ---- Only if necessary
    DynamicForward 8080 # ---- Only if necessary
  ```

  > **NOTE** : Please remove everything including and after `# ----`
