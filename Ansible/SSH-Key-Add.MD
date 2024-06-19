# Ansible Set-Up to use SSH Keys

On Ansible "controller", create ssh key pair using "ssh-keygen" command <br>

More information on ssh-keygen : https://www.linux.org/docs/man1/ssh-keygen.html <br>

```
ssh-keygen

# Use the default location - /home/vagrant/.ssh folder along with the key-name : id_rsa
# Do not use a passphrase

# Keys can be generated any number of times
# Check the keys & verify if a public & private keys are present

cd /home/vagrant/.ssh
ls -la

```

Copy the public key to "ubuntu1" VM using ssh-copy-id command <br>
More information on ssh-copy-id : https://www.linux.org/docs/man1/ssh-copy-id.html <br>

```
ssh-copy-id -i /home/vagrant/.ssh/id_rsa vagrant@192.168.10.20

# Upon successful copy, verify if we can login to ubuntu1 (192.168.10.20) without password

ssh vagrant@192.168.10.20

```

Setup Ansible to use SSH keys using the configuration ansible_ssh_private_key_file <br>

Edit /etc/ansible/hosts file as follows: <br>

```
[ubuntu1]
192.168.10.20

[ubuntu1:vars]
ansible_connection=ssh
ansible_user=vagrant
#ansible_ssh_pass=vagrant --> This can be commented out
ansible_ssh_private_key_file=/home/vagrant/.ssh/id_rsa
```

Check if we can connect to "ubuntu1" now: <br>

```
ansible ubuntu1 -m ping -vvv
```

