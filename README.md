You've heard of [kubespray](https://github.com/kubernetes-sigs/kubespray); the production ready Ansible playbook to manage Kubernetes clusters... Now get ready for:

# kubeleak

The, not-production ready, Ansible playbook to create a vanilla `kubeadm` configured cluster.

Kubeleak is a very light-weight, minimalist way, to deploy a 3 node Kubernetes cluster on Ubuntu 20.04 LTS.

I wanted a very opinionated way to build bare metal Kubernetes using `kubeadm` for side projects. Mainly for the ability to quickly identify every component of the playbook and be able to make adjustments.

>To be extremely clear, kubeleak is NOT better than kubespray, it is just easier to grok. USE AT YOUR OWN RISK!

## Use

`inventory.yaml` -> playbooks/ -> `kubeadmin-config.j2` template -> GitOps.

- [Install](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) Ansible
- Copy SSH keys to each of the nodes
  - Verify a [key exists or create one](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2).
  -   `ssh-copy-id USER@NODE_IP_ADDRESS_1`
  -   `ssh-copy-id USER@NODE_IP_ADDRESS_2`
  -   `ssh-copy-id USER@NODE_IP_ADDRESS_3`
- Clone this repo && cd
- Update `inventory.yaml` with IPs and custom variables
- Test: `ansible all -m ping`
- Run: `ansible-playbook playbooks/update.yaml -K` to update all nodes (general)
  - The `-K` force's ansible to ask you to type the password for sudo, "the BECOME password." An alternative would be to add your user to the [WHEEL](https://www.tecmint.com/run-sudo-command-without-password-linux/) file so it never prompts for a password.
- Run: `ansible-playbook playbooks/set-k8s-version.yaml -K` to update / force the Kubernetes version installed (as set by `kubernetes_version` in `inventory.yaml`).
- Run: `ansible-playbook playbooks/install-k8s.yaml -K` to install Kubernetes using the `templates/kubeadm-config.j2` file.

You now have a basic running `kubeadm` cluster! Log in to the control plane node to run `kubectl` commands or copy the `/etc/kubernetes/admin.conf` kubeconfig.

There's another playbook in there `automate-upgrades.yaml` that is used for another side project of mine (isitstillrunning.com). A lot of the playbook(s) are modified from a [tutorial](https://www.linuxsysadmins.com/install-kubernetes-cluster-with-ansible/) for "Installing Kubernetes Cluster with Ansible on Ubuntu in 5 minutes."

Don't forget to make backups!