# GitOps Workshop Guide for **Sterling File Gateway/B2B Integrator**

## Overview  

<!--- cSpell:ignore gitorg YAMLs -->

The GitOps concept originated from [Weaveworks](https://www.weave.works/) back in 2017 and the goal was to automate the operations of a Kubernetes (K8s) system using a model external to the system as the source of truth ([History of GitOps](https://www.weave.works/blog/the-history-of-gitops)).

There are various GitOps structure and workflows.  This workshop will cover our opinionated point of view (PoV) on how `GitOps` can be used to manage the infrastructure, services and application layers of K8s based systems.  It takes into account the various personas interacting with the system and accounts for separation of duties.

On the first day of the workshop, we will give you an overview of IBM's GitOps structure and workflow. This workshop guide is meant to be a companion for the 2nd and 3rd day of the workshop, where you will get hands-on experience of deploying IBM Sterling File Gateway/B2B Integrator using our GitOps workflow. There are 4 parts to this workshop guide: 
-   Shell Environment Setup - Shell env needs to be setup before starting each lab
-   Pre-Lab 1 - Create your github personal access token, which you'll be using to push to your GitOps repos
-   Lab 1 - Deploy IBM Sterling File Gateway/B2B Integrator using our GitOps process
-   Lab 2 - Validate use case requirements: Self-Healing, Upgrade/Rollback and Horizontal Pod Auto-Scaling.

## Shell Environment Setup 
You will be provided with a spreadsheet which will contain links to your assigned environments, and your associated ids. Before you start this section of the workshop, you will need to verify that you can access your assigned Openshift cluster on IBM Cloud using your IBMid. You will also need to verify that you have been added to a Public GitHub Organisation by the IBM team.

1. Login to your IBM Cloud account and access the [IBM Cloud Shell](https://cloud.ibm.com/shell)

*Note that the shell session's [IBM Cloud Shell workspace](https://cloud.ibm.com/docs/cloud-shell?topic=cloud-shell-files#file-persistence) is deleted one hour after the shell session is closed.  If you lose the shell workspace, you will need to re-setup the environment.*

2. Install and setup the prequiste CLIs 
```bash
mkdir bin
cd bin
wget https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.17.4/kubeseal-0.17.4-linux-amd64.tar.gz
tar -xvf kubeseal-0.17.4-linux-amd64.tar.gz
chmod 755 kubeseal
export PATH=~/bin:$PATH
echo $PATH
```

3. Setup the environement variables
```bash
# Your-Github-Org should be the name of the github org that was created for this Lab
export GIT_ORG=Your-Github-Org
```
```bash
#Validate that GIT_ORG has the correct value.
echo $GIT_ORG 
```
4. Clone your GitOps repositories from your Github Organization 
```bash
cd ~
mkdir $GIT_ORG
cd $GIT_ORG
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops.git
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops-infra.git
git clone https://github.com/$GIT_ORG/multi-tenancy-gitops-services.git
ls -l
```

5. Setup your GitHub profile
```bash
# Your e-mail should be the e-mail you used to sign up for github
git config --global user.email "Your e-mail"
```
```bash
# Your Name should be the name you used to sign up for github
git config --global user.name "Your Name"
```

6. Switch to your assigned Openshift cluster on IBM cloud.
![openshift_cluster](images/openshift-ibm-cloud.png "Screenshot of Openshift Cluster on IBM cloud")

Launch the **OpenShift Web Console**. From the dropdown menu on the upper right corner of the OpenShift web console, select `Copy login command`.

![openshift_web_console](images/openshift-web-console.png "Screenshot of Openshift Web Console")

You should be redirected to a nearly blank page containing a link that says `Display Token`.

![openshift_display_token](images/openshift-display-token.png "Screenshot of Openshift Web Console")

Once you click on `Display Token`, you will be able to see your OpenShift API token as well as the login command for logging into your Openshift cluster on the shell. Copy and paste the line that starts with `oc login` in your IBM Cloud shell.


7. Check the spreadsheet provided prior to this workshop for the link to your assigned Argo CD instance. The spreadsheet should also contain the credentials needed for you to login to the ArgoCD instance as admin. Once you have logged in, verify that you have the `infra` and `services` Argo CD applications:
 
 ![argocd_apps](images/argocd-apps.png "Screenshot of  Argo CD start page")
 
---

### Pre-Lab 1 - Create a GitHub Personal Access Token

1. Log in to the public GitHub account which you are using for this workshop

2. Click on your GitHub avatar located at the top right corner of the page and select `Settings` from the dropdown

![github_settings](images/github-settings.png "Screenshot of GitHub profile dropdown")

3. Check the left sidebar, and scroll down until you see **Developer settings**
![github_dev_settings](images/github-dev-settings.png "Screenshot of Github settings sidebar")

4. Once you have clicked on **Developer settings**, you will see a new left sidebar where you need to select **Personal access tokens**. You will then see a button you can click to `Generate new token`
![github_token_generate](images/github-token-generate.png "Screenshot of Personal Access Token settings page")

5. Enter a name for your new personal access token and set a suitable `Expiration`. Ensure the **repo** scope is checked before you click **Generate Token** at the bottom of the page
![github_token_scope](images/github-token-scope.png "Screenshot of Personal Access Token scope")

6. The token is displayed only once so, make sure you copy it. You will need it multiple times during the next parts of the workshop.
![github_token](images/github-token.png "Screenshot of GitHub Personal Access Token result")

---

## Lab 1 - Deploy the  IBM Sterling File Gateway/B2B Integrator using IBM's GitOps Recipe


### Deploy the Pre-Requisite Infrastructure Components 
In the first section of this lab, you will review the  `Infrastructure` layer in the [kustomization.yaml](https://github.com/cloud-native-toolkit/multi-tenancy-gitops/blob/master/0-bootstrap/single-cluster/1-infra/kustomization.yaml) file and un-comment the resources  based on the  [IBM SFG recipe](https://github.com/cloud-native-toolkit/multi-tenancy-gitops/blob/master/doc/sfg-recipe.md) as below.

### 1. Edit the Infrastructure layer - Kustomization.yaml file

Open the kustomization.yaml file for the infra layer as follows
```bash
vi ~/$GIT_ORG/multi-tenancy-gitops/0-bootstrap/single-cluster/1-infra/kustomization.yaml
```

You'll need to un-comment some of the k8s resources under the 'resources' field in the `kustomization.yaml` file in order to deploy the kubernetes infrastructure level resources required for Sterling B2Bi. The resources you'll need to uncomment are shown below:

```Markdown
resources:
#- argocd/consolelink.yaml
- argocd/consolenotification.yaml
#- argocd/namespace-ibm-common-services.yaml
#- argocd/namespace-ci.yaml
#- argocd/namespace-dev.yaml
#- argocd/namespace-staging.yaml
#- argocd/namespace-prod.yaml
#- argocd/namespace-cloudpak.yaml
#- argocd/namespace-istio-system.yaml
#- argocd/namespace-openldap.yaml
- argocd/namespace-sealed-secrets.yaml
#- argocd/namespace-tools.yaml
#- argocd/namespace-instana-agent.yaml
#- argocd/namespace-robot-shop.yaml
#- argocd/namespace-openshift-serverless.yaml
#- argocd/namespace-knative-eventing.yaml
#- argocd/namespace-knative-serving.yaml
#- argocd/namespace-knative-serving-ingress.yaml
#- argocd/namespace-openshift-storage.yaml
#- argocd/namespace-spp.yaml
#- argocd/namespace-spp-velero.yaml
#- argocd/namespace-baas.yaml
#- argocd/namespace-db2.yaml
#- argocd/namespace-mq.yaml
- argocd/namespace-b2bi-prod.yaml
#- argocd/namespace-b2bi-nonprod.yaml
#- argocd/serviceaccounts-ibm-common-services.yaml
#- argocd/serviceaccounts-tools.yaml
#- argocd/serviceaccounts-db2.yaml
#- argocd/serviceaccounts-mq.yaml
- argocd/serviceaccounts-b2bi-prod.yaml
#- argocd/serviceaccounts-b2bi-nonprod.yaml
- argocd/sfg-b2bi-clusterwide.yaml
#- argocd/scc-wkc-iis.yaml
#- argocd/norootsquash.yaml
- argocd/daemonset-sync-global-pullsecret.yaml
#- argocd/storage.yaml
#- argocd/infraconfig.yaml
#- argocd/machinesets.yaml
```

Now deploy these changes by committing and pushing the changes to your `multi-tenancy-gitops` repository:
```bash
#change to the `multi-tenancy-gitops` directory
cd ~/$GIT_ORG/multi-tenancy-gitops

# Verify the changes, and add the files that have been changed
git status
git add -u
 
# Finally commit and push the changes
git commit -m "for now only deploy infrastructure resources"
git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```

Sync the changes in Argo at via the `infra` argo application
*** Need a picture here***


### 2. Install the Sealed Secret Service

Edit the Argo Services layer in the `multi-tenancy-gitops` **repo**   

```
vi ~/$GIT_ORG/multi-tenancy-gitops/0-bootstrap/single-cluster/2-services/kustomization.yaml
```

Install Sealed Secrets service by uncommenting the line below:

```Markdown
resources:

# Sealed Secrets
- argocd/instances/sealed-secrets.yaml

```

Now deploy the sealed-secrets service by committing and pushing the changes to your `multi-tenancy-gitops` repository:
```bash
#change to the `multi-tenancy-gitops` directory
cd ~/$GIT_ORG/multi-tenancy-gitops

# Verify the changes, and add the files that have been changed
git status
git add -u

# Finally commit and push the changes
git commit -m "only deploy the sealed secret service"

git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```
Sync the changes in Argo via the service argo application

### 3. Generate Sealed Secrets and  Volume Storage Resources required by Sterling File Gateway

Now in the `multi-tenancy-gitops-service` **repo**, change to the  B2B setup directory to generate the sealed secret and the volume storage deployment yaml files.
```bash
cd ~/$GIT_ORG/multi-tenancy-gitops-services/instances/ibm-sfg-b2bi-prod-setup
```

#### Generate Sealed Secrets resources required by Sterling File Gateway.  
The following commands will generate the yaml resource files from a template and create the deployment yaml files to deploy the sealed secrets into the cluster.

Generate a Sealed Secret for the credentials. Execute the following commands:
```bash
B2B_DB_SECRET=db2inst1 \
JMS_PASSWORD=password JMS_KEYSTORE_PASSWORD=password JMS_TRUSTSTORE_PASSWORD=password \
B2B_SYSTEM_PASSPHRASE_SECRET=password \
./sfg-b2bi-secrets.sh
```

#### Generate Persistent Volume Storage resources required by Sterling File Gateway. 
The following commands will generate the yaml resource files from a template and create the deployment yaml files to deploy the volume storage into the cluster.  Execute the command: 

```bash
./sfg-b2bi-pvc-mods.sh
```

Now deploy the generated resources changes by committing and pushing the changes to your `multi-tenancy-gitops-services` repository:
```bash
# Verify the changes by with the following command.  You should see new yaml files for the sealed secrets and volume storage yamls
git status

# Need to add the generated yaml files to git staging area
git add .

# Finally commit and push the changes
git commit -m "deploy resources needed for service"

git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```

### 4. Enable DB2, MQ and prerequisites in the main multi-tenancy-gitops repository
Edit the Services layer ${GITOPS_PROFILE}/2-services/kustomization.yaml by uncommenting the following lines to install the pre-requisites for Sterling File Gateway:

```
vi ~/$GIT_ORG/multi-tenancy-gitops/0-bootstrap/single-cluster/2-services/kustomization.yaml
```
```Markdown
resources:

# B2BI
- argocd/instances/ibm-sfg-db2-prod.yaml
- argocd/instances/ibm-sfg-mq-prod.yaml
- argocd/instances/ibm-sfg-b2bi-prod-setup.yaml
#- argocd/instances/ibm-sfg-b2bi-prod.yaml
#- argocd/instances/ibm-sfg-db2-nonprod.yaml
#- argocd/instances/ibm-sfg-mq-nonprod.yaml
#- argocd/instances/ibm-sfg-b2bi-nonprod-setup.yaml
#- argocd/instances/ibm-sfg-b2bi-nonprod.yaml
```

Now deploy the resources changes by committing and pushing the changes to your `multi-tenancy-gitops` repository:
```bash
#change to the `multi-tenancy-gitops` directory
cd ~/$GIT_ORG/multi-tenancy-gitops

# Verify the changes, and add the files that have been changed
git status
git add -u

# Finally commit and push the changes
git commit -m "deploy services resources"

git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```

Sync the changes in Argo  via the `service` argo application


### 5. Create the B2B Installation settings for deployment

Generate the installation settings in the `multi-tenancy-gitops-services` **repo**  by executing the following commands:

```bash
cd ~/$GIT_ORG/multi-tenancy-gitops-services/instances/ibm-sfg-b2bi-prod

./ibm-sfg-b2bi-overrides-values.sh
```

Update the  **repo** by committing and pushing the changes to your `multi-tenancy-gitops-service` repository:
```bash
# Verify the changes by with the following command.  You should see new values.yaml file generated
git status

# Need to add the generated yaml file to git staging area
git add .

# Finally commit and push the changes
git commit -m "created the deployment settings via the values.yaml"

git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```

### 6. Deploy the IBM Sterling B2B Integrator 

Edit the Argo Services layer in the `multi-tenancy-gitops` **repo**  and install the IBM Sterling B2B Integrator Service 

```
vi ~/$GIT_ORG/multi-tenancy-gitops/0-bootstrap/single-cluster/2-services/kustomization.yaml
```
```Markdown
resources:

# B2BI
- argocd/instances/ibm-sfg-db2-prod.yaml
- argocd/instances/ibm-sfg-mq-prod.yaml
- argocd/instances/ibm-sfg-b2bi-prod-setup.yaml
- argocd/instances/ibm-sfg-b2bi-prod.yaml
#- argocd/instances/ibm-sfg-db2-nonprod.yaml
#- argocd/instances/ibm-sfg-mq-nonprod.yaml
#- argocd/instances/ibm-sfg-b2bi-nonprod-setup.yaml
#- argocd/instances/ibm-sfg-b2bi-nonprod.yaml
```


Now deploy the IBM Sterling B2B Integrator Service   by committing and pushing the changes to your `multi-tenancy-gitops` repository:
```bash
#change to the `multi-tenancy-gitops` directory
cd ~/$GIT_ORG/multi-tenancy-gitops

# Verify the changes, and add the files that have been changed
git status
git add -u

# Finally commit and push the changes
git commit -m "deploy the IBM Sterling B2B Integrator servcie"
git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```

Sync the changes in Argo  via the `service` argo application

**Note that the above sync will take approximately 1.5 hours as this part of the deployment generates the initial Sterling B2B Integrator database.**

Now verify the the Sterling File Gateway Console.  Retrieve the Sterling File Gateway console URL.
```bash
oc get route -n b2bi-prod ibm-sfg-b2bi-sfg-asi-internal-route-dashboard -o template --template='https://{{.spec.host}}'
```
and login with the default credentials:  username:`fg_sysadmin` password: `password` 



___

## Lab 2 - Validate the Use Cases for Self-Healing, Upgrade/Rollback and automatic Pod Scaling
The final part of the hands-on lab is to validate the three use cases (Self-Healing, Upgrade/RollBack, and Automatic Pod Scaling).

See the [use case instructions](./Scenarios.md) to complete final part of the workshnop.

---
