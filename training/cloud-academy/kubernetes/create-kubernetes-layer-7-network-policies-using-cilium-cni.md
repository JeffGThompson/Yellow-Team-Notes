# Create Kubernetes Layer-7 Network Policies using Cilium CNI



## Connecting to the CloudAcademy Web based K8s IDE <a href="#lab-page-title" id="lab-page-title"></a>

In the AWS Management Console search bar, enter _EC2_, and click the **EC2** result under **Service.** In the left-hand menu, click on **Instances**, select the _k8s.cluster.cloudacademy.platform.instance_ EC2 instance, and locate and copy the _IPv4 Public IP_ address.

<figure><img src="../../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>

The web-based CloudAcademy IDE has been configured to listen for inbound connections on port **3000** using HTTP. Using your browser, navigate to the IDE hosted on the _k8s.cluster.cloudacademy.platform.instance_ EC2 instance using the public IP address you just copied:

```
http://PUBLIC_IP_IDE_CLOUDACADEMY_PLATFORM_INSTANCE:3000
```

<figure><img src="../../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

## Install Cilium CNI <a href="#lab-page-title" id="lab-page-title"></a>

Within the Files Explorer pane right-click on the **project/code/cillium** folder and then select the **Open in Terminal** option to launch the integrated terminal.

<figure><img src="../../../.gitbook/assets/image (2) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the **kubectl** command to install the Cilium Kubernetes CNI plugin into the local Kubernetes cluster. Within the terminal enter the following command.

```
kubectl apply -f install.1.6.1.yaml
```

Use both **watch** and **kubectl** to view the Cilium pods starting up. Within the terminal enter the following commands.

```
watch -n2 kubectl -n kube-system get pods
```

Once all Cilium Pods have reached the **running** status, exit the previous command by entering the key sequence CTRL+C.

<figure><img src="../../../.gitbook/assets/image (4) (3).png" alt=""><figcaption></figcaption></figure>

## Deploy API Pods <a href="#lab-page-title" id="lab-page-title"></a>

Within the **Files** pane, open the **lab-code/deploy-api.yaml** file within the editor. Take some time to review the Kubernetes resources that are going to be provisoned within the cluster.

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Ensure that you are in the **lab-code** directory. Within the terminal run the following command

```
cd /home/project/code/StarWars/lab-code
```

You will now create all of the Star Wars API resources declared within the **deploy-api.yaml** file. Within the terminal run the following commands

```
kubectl apply -f deploy-api.yaml
```

Confirm that all of the pods have launched successfully. Within the terminal run the following command

```
kubectl get pods
```

<figure><img src="../../../.gitbook/assets/image (2) (2) (1).png" alt=""><figcaption></figcaption></figure>

Confirm that the service has launched successfully. Within the terminal run the following commands

```
kubectl get services
```

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

## Test API Before Layer-7 Network Policy is Deployed <a href="#lab-page-title" id="lab-page-title"></a>

Retrieve the DeathStar Service VIP assigned to the API. Within the terminal run the following command

```
DEATHSTAR_VIP=`kubectl get service/deathstar -o jsonpath='{.spec.clusterIP}'`
echo $DEATHSTAR_VIP 
```

<figure><img src="../../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Perform a curl request that originates from **tiefighter** pod and is sent to the /v1 API service endpoint. This should display all of the available API endpoints. Within the terminal run the following commands

```
kubectl exec -it tiefighter -- curl -is -XGET http://$DEATHSTAR_VIP/v1
```

<figure><img src="../../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Now perform a curl request that originates from **tiefighter** pod and is sent to the /v1/request-landing API service endpoint. Within the terminal run the following commands. Notice the response which indicates success - which is expected.

```
kubectl exec -it tiefighter -- curl -is -XPOST http://$DEATHSTAR_VIP/v1/request-landing
```

<figure><img src="../../../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

Now perform a curl request that originates from **xwing** pod and is sent to the same /v1/request-landing API service endpoint. Within the terminal run the following commands. Notice the response which indicates success - which is NOT expected.

```
kubectl exec -it xwing -- curl -is -XPOST http://$DEATHSTAR_VIP/v1/request-landing
```

<figure><img src="../../../.gitbook/assets/image (6) (2).png" alt=""><figcaption></figcaption></figure>

## Secure API with Layer-7 Network Policy <a href="#lab-page-title" id="lab-page-title"></a>

Within the **Files** pane, open the **lab-code/l7-networkpolicy.yaml** file within the editor. Take some time to review the Layer-7 rules defined within the Network Policy

<figure><img src="../../../.gitbook/assets/image (10) (3).png" alt=""><figcaption></figcaption></figure>

Ensure that you still in the **lab-code** directory. Within the terminal run the following commands.

```
cd /home/project/code/StarWars/lab-code
```

You will now create all of the Star Wars API resources declared within the **deploy-api.yaml** file. Within the terminal run the following commands.

```
kubectl apply -f l7-networkpolicy.yaml
```

Confirm that the Cilium Network Policy has been deployed successfully. Within the terminal run the following commands.

```
kubectl describe cnp
```

<figure><img src="../../../.gitbook/assets/image (7) (3).png" alt=""><figcaption></figcaption></figure>

## Test API After Layer-7 Network Policy is Deployed <a href="#lab-page-title" id="lab-page-title"></a>

Retrieve the DeathStar Service VIP assigned to the API. Within the terminal run the following command.

```
DEATHSTAR_VIP=`kubectl get service/deathstar -o jsonpath='{.spec.clusterIP}'`
echo $DEATHSTAR_VIP 
```

<figure><img src="../../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Now perform a curl request that originates from **tiefighter** pod and is sent to the /v1/request-landing API service endpoint. Within the terminal run the following commands. Notice the response which indicates success - which is expected.

```
kubectl exec -it tiefighter -- curl -i --connect-timeout 10 -XPOST http://$DEATHSTAR_VIP/v1/request-landing
```

<figure><img src="../../../.gitbook/assets/image (17) (3).png" alt=""><figcaption></figcaption></figure>

Now perform a curl request that originates from **xwing** pod and is sent to the same /v1/request-landing API service endpoint. Within the terminal run the following commands. otice now that no response is given - this is based on the previously deployed network policy now blocking this type of traffic - which is what we want.

```
kubectl exec -it xwing -- curl -i --connect-timeout 10 -XPOST http://$DEATHSTAR_VIP/v1/request-landing
```

<figure><img src="../../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

