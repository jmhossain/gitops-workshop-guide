# CP4I Capabilities - Use Case walkthrough

## 1. Self-healing

In this section of the lab, we see how RedHat OpenShift performs self healing when a pod is deleted. 

To delete the pod, login to your cluster with your IBMid by browsing to the `OpenShift web console` (*see your environment assignment e-mail for the link to your ROKS Cluster URL*).  From the administrator section menu on the left, clink on the `Workload` drop down menu and click on Pods, and at the top, select the `tools` project.  Select one of the `ibm-sfg-b2bi-sfg` pods to delete and end of the row, click the vertical dot menu and click delete. 

  ![version](images/Delete-a-pod.png "Screenshot of Deletion")

      
After the pod is deleted, the pod is reinstantiated and processing work as part of the deployed Sterling B2B Integrator cluster.

    >💡 **NOTE**     
    > While the pod is being terminated a new pod is being created.
    > If you delete on one of the pods or it crashes Openshift will automatically create a new pod to replace the problem pod
      
  ![verion](images/terminat.png "Screenshot of termination")
      
  ![verion](images/pod-up.png "Screenshot of termination")

---
