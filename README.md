# flask-ansible-task
This is an example Flask app to be deployed with an Ansible playbook.

The included deploy playbook will:

- Install system apt packages:
    - python3-pip
    - python3-dev
    - python3
    - nginx
    - gunicorn
    - virtualenv
- Clone the repo:
    - https://github.com/D-Rex-III/flask-ansible-task.git
- Install Python requirements in a virtualenv:
    - Installs requirements.txt in the app env.
- Configure gunicorn, nginx, ufw and systemd:
    - Ansible-Playbook will copy, template replace variables, and set up .service and .nginx.
- Enable and start services:
    - This step will set up our new service and nginx server.
- Check the `/tmp/index.html` url for expected response:
    - Finally it will check the app created index.html for a response.

## Prerequisites
You'll need [Ansible installed](https://docs.ansible.com/ansible/latest/intro_installation.html) and SSH access to any hosts. In this example I previously set up an AWS EC2 instance with a .pem file and used the public ip to customize the `.hosts` file as needed.
```
pip install ansible
git clone https://github.com/D-Rex-III/flask-ansible-task.git
cd flask-ansible-task
nano .hosts
```
Change `<public_ip>` `<public_ip_ssh_port>` `<remote_user>` `<public_pem_location_used_to_set_up_remote>` in `.hosts` to match your running host and save.

## Deploying the app
Now run the deploy playbook. This will use the `deploy.yaml` file and `.hosts` file.
```
ansible-playbook deploy.yml
```
If having trouble make sure you're in the `flask-ansible-task` folder and run
```
ansible-playbook -i .hosts deploy.yml
```
## Debugging
If the previous commands are returning an error rerun the playbook with verbosity. This will give you much more detail on each step being run by the `deploy.yml` and lead you in the right direction.
```
ansible-playbook deploy.yml -vvvv
```

If you're connection to the host in `.hosts` is working fine but your website isn't ssh into the host and check the logs on the host.
```
journalctl -xe
journalctl -u nginx
journalctl -u flask-ansible-task
```

Also check the systemd status of the services on the host.
```
sudo systemctl status nginx
sudo systemctl status flask-ansible-task.service
```