# Cloudflare Tunnel

https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/

## Description

Setup a Cloudflare Tunnel for your Kubernetes Services

## Source

https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/deployment-guides/kubernetes/

## Sample Security and Authentication

### TOKEN

Without a valid token its not possible to deploy this, you need a Clouflare Zero Trust Account and add a new tunnel there, then you get the needed token! 


### Setup TOKEN as Sealed Secrets

You have to have Selead Secret operator running for Sealed Secrets to work!

Generate all needed Secrets locally and seal them, so that you are able to store it in GIT. Just store the yaml Files with the sealed Secrets,
do not save your Real Passwords or the Secrets from Kubernetes, they are just Base64 Encoded, its easy to decode and therefore not safe!
For sealing you have to install Sealed Secrets from Bitnami. Find a tutorial with Kustomize and Argo CD in my repo [Sealed Secrets Kustomize](https://github.com/wep4you/sealed-secrets-kustomize) 


Now generate a Sealed Secret for your token, this new generated file could be checked in.

    kubectl --namespace cloudeflare create secret generic cloudeflare-tunnel --dry-run=client \
    --from-literal token='<YOUR-CLOUDEFLARE-TUNNEL-TOKEN>' \
    --output json | kubeseal | tee overlays/local/sealedsecret.yml    


## Install with Argo CD

Use a central repository which links to all Apps which has to be deployed,
and add the App desciption there, or deploy the App for heimdall manually.
Find a sample in my [App of Apps Repo](https://github.com/wep4you/k8s-apps.git),
there is also sample definition for the [Heimdall App](https://github.com/wep4you/k8s-apps/blob/main/local/CloudflareTunnel.yml)


## Install manual with kubectl

To add your own configuration, copy ```overlays/local``` to a new folder and change the files to your needs.
It's standard Kustomize format, in the example Ingress is patched with a new Domainname and the Deployment
is patched with another Version of the Image, instead of latest from the base file.

    ```
    kubectl apply -k overlays/local
    ```
