## Configure Hybrid networking 
Before we can enable windows container support on the cluster we need to configure Hybrid networking. Run command below. Read more about this [here](https://docs.openshift.com/container-platform/4.13/networking/ovn_kubernetes_network_provider/configuring-hybrid-networking.html)

```
oc patch networks.operator.openshift.io cluster --type=merge \
  -p '{
    "spec":{
      "defaultNetwork":{
        "ovnKubernetesConfig":{
          "hybridOverlayConfig":{
            "hybridClusterNetwork":[
              {
                "cidr": "10.132.0.0/14",
                "hostPrefix": 23
              }
            ]
          }
        }
      }
    }
  }'
```
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
ansible-playbook install/add-windows-workers.yaml --extra-vars=key=$PRIVATE_KEY
```

Deploy a sample app to verify running windows containers. First create a runtime class by running command below. This makes it easy to ensure workload gets scheduled on windows nodes.

```
oc apply -f apps/runtimeclass.yaml
```

Next lets deploy a sample .net framework app by running command below

```
oc apply -f apps/deployment.yaml
```

Next lets create a service by running command below

```
oc apply -f apps/service.yaml
```

Command below creates a service with type load balancer so we can just test out accessing the app from outside the cluster. Run command below to get the load balancer endpoint

```
export SERVICE_URL=$(oc get services win-webserver -o jsonpath="{.status.loadBalancer.ingress[0].hostname}")
```

Run CURL command below to verify you can successfully access the containerized windows app running on windows worker node in OpenShift

*note:* make sure container is running before running curl. It little bit of time for container to come up on windows node :) 

```
curl http://$SERVICE_URL
```

You should see output as below

```
<html><body><H1>Red Hat OpenShift + Windows Container Workloads</H1></body></html>
```




