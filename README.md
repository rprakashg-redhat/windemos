
## Enabling Windows Container Support in OpenShift

Run command below to generate a pem encoded RSA private key

```
openssl genrsa -out ocpwin.key 2048
```

Run command below to Base64 encode the RSA private key to create secret when enabling windows container support in OpenShift 

```
export PRIVATE_KEY=$(cat {{ replace/with/path/to/pem/encoded/privatekey }} | base64)
```

Enable windows container support by running command below

```
helm upgrade -i enable-windows-support install/helm/enable-windows-support --set key=$PRIVATE_KEY
```

Finding AMId to use with windows worker nodes

```
$(aws ec2 describe-images --region us-west-2 --filters "Name=name,Values=Windows_Server-2019*English*Full*Containers*" "Name=is-public,Values=true" --query "reverse(sort_by(Images, &CreationDate))[*].{name: Name, id: ImageId}" --output json
```

Run the command below to add windows worker nodes to the cluster

```
helm upgrade -i add-windows-workers install/helm/add-windows-workers
```

How to base64 decode private key data stored in secret that Windows Machine Config Operator (WMCO) will use to communicate with Windows nodes

```
oc get secrets cloud-private-key  -o 'go-template={{index .data "private-key.pem"}}' | base64 -d
```

