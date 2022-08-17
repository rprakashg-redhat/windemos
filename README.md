
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
helm upgrade -i enable-windows-support install/helm --set key=$PRIVATE_KEY
```

How to base64 decode private key data stored in secret that Windows Machine Config Operator (WMCO) will use to communicate with Windows nodes

```
oc get secrets cloud-private-key  -o 'go-template={{index .data "private-key.pem"}}' | base64 -d
```

