```sh
# deploy staging
ansible-playbook kube-books/certs-mgr.yaml

# deploy production
ansible-playbook kube-books/certs-mgr.yaml -e '{"production":true}'

# reset/delete staging
ansible-playbook kube-books/certs-mgr.yaml -e '{"reset":true}'

# reset/delete production
ansible-playbook kube-books/certs-mgr.yaml -e '{"reset":true, "production":true}'
```