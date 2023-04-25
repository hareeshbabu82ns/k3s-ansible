### pre install
* clone the repo `https://github.com/hareeshbabu82ns/k3s-ansible.git`

* create `vault.pass` file on root

* create `virtual env` for ansible
```sh
python3 -m venv .venv
source .venv/bin/activate
```

* install dependencies
```sh
ansible-galaxy collection install -r ./collections/requirements.yml
pip install -r requirements.in
```

* copy `ansible.example.cfg` to `ansible.cfg` to adapt to the path of hosts file
* copy `sample` folder to `<my-cluster>` to adapt to env
* update `<my-cluster>/hosts.yml` with ips
* update `<my-cluster>/groups_vars/all.yml` with ips
```yaml
ansible_user: <ssh_user_to_vm>
system_timezone: "America/Edmonton"
apiserver_endpoint: "192.168.30.222" # vip
k3s_token: "some-SUPER-DEDEUPER-secret-password"
metal_lb_ip_range: "192.168.30.80-192.168.30.90"
proxmox_lxc_ct_ids:
  - 111
  - 222
```

* install the cluster
```sh
ansible-playbook site.yml -i inventory/<my-cluster>/hosts.yml

# validate vip
ping 192.168.30.222

# reset cluster
ansible-playbook reset.yml -i inventory/<my-cluster>/hosts.yml
```

* copy `kube config` to local
```sh
mkdir ~/.kube
scp km1:/etc/rancher/k3s/k3s.yaml ~/.kube/config
echo 'source <(kubectl completion bash)' >>~/.bashrc
echo 'alias k=kubectl' >>~/.bashrc
echo 'complete -o default -F __start_kubectl k' >>~/.bashrc

vi ~/.kube/config # update ip to point to vip
```

* basic [test](https://docs.technotim.live/posts/k3s-etcd-ansible/#testing-your-cluster) with simple nginx deployment
```sh
kubectl apply -f example/deployment.yml
kubectl describe deployment nginx

kubectl apply -f example/service.yml

# delete after test
kubectl delete -f example/deployment.yml
kubectl delete -f example/service.yml
```