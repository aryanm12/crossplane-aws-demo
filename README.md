Install Crossplane:
-------------------

Use Helm 3 to install the latest official stable release of Crossplane, suitable for community use and testing:
-------------------------------------------------------------------------------------------------------

kubectl create namespace crossplane-system

helm repo add crossplane-stable https://charts.crossplane.io/stable
helm repo update

helm install crossplane --namespace crossplane-system crossplane-stable/crossplane


Check Crossplane Status:
-------------------------

helm list -n crossplane-system

kubectl get all -n crossplane-system



Install Crossplane CLI:
-----------------------

curl -sL https://raw.githubusercontent.com/crossplane/crossplane/master/install.sh | sh


Get AWS Account Keyfile
Using an AWS account with permissions to manage RDS databases:
---------------------------------------------------------------

AWS_PROFILE=default && echo -e "[default]\naws_access_key_id = $(aws configure get aws_access_key_id --profile $AWS_PROFILE)\naws_secret_access_key = $(aws configure get aws_secret_access_key --profile $AWS_PROFILE)" > creds.conf


Create a Provider Secret:
---------------------------

kubectl create secret generic aws-creds -n crossplane-system --from-file=creds=./creds.conf


Installing Providers:
------------------------
```
apiVersion: pkg.crossplane.io/v1
kind: Provider
metadata:
  name: provider-aws
spec:
  package: "crossplane/provider-aws:v1.9.0"
```

kubectl apply -f crossplane-aws-provider.yaml


We will create the following ProviderConfig object to configure credentials for AWS Provider and add the contents in the file aws-providerconfig.yaml:
-----------------------------------------------------------------------------------------------------

```
apiVersion: aws.crossplane.io/v1beta1
kind: ProviderConfig
metadata:
  name: default
spec:
  credentials:
    source: Secret
    secretRef:
      namespace: crossplane-system
      name: aws-creds
      key: creds
```

kubectl apply -f aws-providerconfig.yaml


Create Postgres RDS instance:
-----------------------------

kubectl apply -f crossplane-postgres.yaml

Create VPC SG:
--------------

kubectl apply -f 
