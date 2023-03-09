
## Enabling Windows Container Support in OpenShift

Generate ssh key
```
ssh-keygen  -t ed25519 -C "$USER@aws-win" -f ~/.ssh/aws-win -N ""
```

Run command below to Base64 encode the private key to create secret when enabling windows container support in OpenShift 

```
export PRIVATE_KEY=$(cat $HOME/.ssh/aws-win | base64)
```

Run ansible playbook names add-windows-workers under install directory to add windows worker nodes to an openshift cluster. See command below. Replace the parameters with values that apply to your environment

```
ansible-playbook install/add-windows-workers.yaml --extra-vars=username={{replace}} --extra-vars=password={{replace}} --extra-vars=apiserver={{replace}} --extra-vars=key=$PRIVATE_KEY
```


