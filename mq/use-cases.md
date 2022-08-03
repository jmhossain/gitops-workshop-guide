# CP4I Capabilities - Use Case walkthrough

## 1. Self-healing

In this section of the lab, we see how RedHat OpenShift performs self healing when a pod is deleted. 

To delete the pod, login to your cluster with your IBMid by browsing to the `OpenShift web console` (*see your environment assignment e-mail for the link to your ROKS Cluster URL*).  From the administrator section menu on the left, clink on the `Workload` drop down menu and click on Pods, and at the top, select the `tools` project.  Select one of the `integration-navigator-ibm` pods to delete. You will find a vertical dot menu  and click delete. 

  ![pod_delete](images/pod-delete.png "Screenshot of Deletion")

      
After the pod is deleted, the pod is reinstantiated and processing work as part of the deployed platform navigator instance.

    >💡 **NOTE**     
    > While the pod is being terminated a new pod is being created.
    > If you delete one of the pods or it crashes, Openshift will automatically create a new pod to replace the problem pod
      
![pod_terminate](images/pod-terminate.png "Screenshot of termination")
      
![pod_heal](images/pod-heal.png "Screenshot of termination")
  
## 2. Upgrade/Rollback 

In this section of the lab, we see how you can upgrade and roll back the MQ operator version using the GitOps method.

Using the GitOps method we see how the upgrade process is shortened.  We are also able to roll back to previous version if there is an issue.  The GitOps method also provides for traceability as to when and who made the change in the commit record in GitHub.

First, check the installed operators on your Openshift cluster. You can do this by opening your Openshift web console and selecting the Operators dropdown then Installed Operators on the left sidebar of the web console.

![operator_version](images/operator-version.png "Screenshot of version")

Now, switch to your IBM Cloud Shell, and open the `ibm-mq-operator.yaml` file on your main gitops repo as follows:
```bash
vi ~/$GIT_ORG/multi-tenancy-gitops/0-bootstrap/single-cluster/2-services/argocd/operators/ibm-mq-operator.yaml
```

Inside the `ibm-mq-operator.yaml` file, you can change the MQ operator version as follows
```yaml
....
spec:
  ....
  source:
    path: operators/ibm-mq-operator
    helm:
      values: |
        ibm-mq-operator:
          subscriptions:
            ibmmq:
              name: ibm-mq
              subscription:
                channel: v1.6     <----change to your desired operator version 
```

Now deploy the changes by committing and pushing the changes to your `multi-tenancy-gitops` repository:
```bash
#change to the `multi-tenancy-gitops` directory
cd ~/$GIT_ORG/multi-tenancy-gitops

# Verify the changes, and add the files that have been changed
git status
git add -u
 
# Finally commit and push the changes
git commit -m "upgrade MQ operator version"
git push
# Input your github username when prompted for Username
# Input the Github Token that you had created earlier when prompted for Password
```

Switch to your Argo CD instance, and click on the **services** application. Click on **REFRESH** located in the top middle of the page. Argo CD will then see the changes you made and automatically propagate the changes to OpenShift.

---
