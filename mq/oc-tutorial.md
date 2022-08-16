### About the OpenShift CLI
With the OpenShift command-line interface (CLI), the oc command, you can create applications and manage OpenShift Container Platform projects from a terminal. The OpenShift CLI is ideal in the following situations:
* Working directly with project source code
* Scripting OpenShift Container Platform operations
* Managing projects while restricted by bandwidth resources and the web console is unavailable

### Installing oc by using the OpenShift mirror repo
1. Download oc from ![OpenShift mirror repo](https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/)
2. Click the binary for your platform
3. Unpack and unzip the archive.
4. Move the oc binary to a directory on your PATH. To check your PATH, open a terminal and execute the following command:
```bash
echo $PATH
```

 ### Installing oc by using the OpenShift Web Console
 You can install the OpenShift CLI (oc) binary on macOS by using the following procedure:
 1. From the web console, click ?.
 ￼
 2. Click Command Line Tools.
 ￼
 3. Select the oc binary for macOS platform, and then click Download oc for Mac for x86_64.
 4. Save the file.
 5. Unpack and unzip the archive.
 6. Move the oc binary to a directory on your PATH. To check your PATH, open a terminal and execute the following command:
 ```bash
 echo $PATH
 ```

 ### Installing oc by using Homebrew (macOS only)
 For macOS, you can install the OpenShift CLI (oc) by using the Homebrew package manager.
 * (pre-requisite) You must have Homebrew (brew) installed. You can install Homebrew by executing the following command:
 ```bash
 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
 ```
 * (procedure) Run the following command to install the openshift-cli package:
 ```bash
 brew install openshift-cli
 ```

### Using oc command
After you have installed the OpenShift CLI, it is available using the oc command, e.g.: 
```bash
oc help
```
Use the above command to get a list and description of all available CLI commands

### Login to your OpenShift Cluste using oc
In order to be able to interact with your OpenShift cluster using the OpenShift CLI, you must firsst login to the cluster.
Switch to your assigned Openshift cluster on IBM cloud.

![openshift_cluster](images/openshift-ibm-cloud.png "Screenshot of Openshift Cluster on IBM cloud")

Launch the **OpenShift Web Console**. From the dropdown menu on the upper right corner of the OpenShift web console, select `Copy login command`. 

![openshift_web_console](images/openshift-web-console.png "Screenshot of Openshift Web Console")

You should be redirected to a nearly blank page containing a link that says `Display Token`.

![openshift_display_token](images/openshift-display-token.png "Screenshot of Openshift Web Console")

Once you click on `Display Token`, you will be able to see your OpenShift API token as well as the login command for logging into your Openshift cluster on the shell. Copy and paste the line that starts with `oc login` in your IBM Cloud shell.

### Working with projects
When viewing projects, you are restricted to seeing only the projects you have access to view based on the authorization policy.

To view a list of projects, run:
```bash
oc get projects
```

You can view the current project that you are on by using the following command:
```bash
oc project
```

You can change from the current project to a different project for CLI operations. The specified project is then used in all subsequent operations that manipulate project-scoped content.
```bash
oc project <project_name>
```

You can create and switch to a new project called 'test' using the following command:
```bash
oc new-project test
```

You may view the status of your current project using the following command:
```bash
oc status
```
You may use the `oc get` command to return the list of objects for a specified object type in your current project:
```bash
oc get <object_type> [<object_name>]
```
If the optional <object_name> is included in the request, then the list of results is filtered by that value.

Below is the list of the most common object types the CLI supports, some of which have abbreviated syntax:
| Object Type | Abbreviated Version |
| :-----: | :-: |
| Deployments | deploy |
| Jobs |  |
| CronJobs | cj |
| Pods | po|
| Secrets |  |
| Services | svc |
| StatefulSets | sts |
| PersistentVolumes | pv |
| PersistentVolumeClaims | pvc |

If you want to know the full list of resources the server supports, use oc api-resources.

### Creating and debugging pod
You may use the following yaml code to create a pod using the OpenShift CLI
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-world
  labels:
    app: hello-world
  namespace: test
spec:
  containers:
    - name: hello-world
      image: 'pvermeyden/nodejs-hello-world'
      ports:
        - containerPort: 80
```
Save the yaml code to a file and call the file `pod.yaml`.
You can then use the following command to create the pod:
```bash
oc create -f pod.yaml
```

In order to check  whether your pod was created, you may use the following command:
```bash
oc get pods
```
You will see underneath the `STATUS` field for your pod `ErrImagePull`. In order to get more insight into the error, you may want to check logs for the pod by using the following command
```bash
oc logs hello-world
```
You will not, however, find any logs since the above error implies that the hello-world image cannot be pulled into your pod and thus, there is no container yet in the pod that can produce logs.

There is a way to check why the pod is unable to pull in the hello-world image, and that is to use the `oc describe` command to return information about your specific object that was created. 
```bash
oc describe pod hello-world
```

You will see under the events section of your output from `oc describe` that there has been multiple Failed Events. In one of the Failed Events, you will see this message `Error reading manifest latest in docker.io/pvermeyden/nodejs-hello-world: manifest unknown`. This is a common error which says that your image does not have the correct tag that you are trying to pull. Search for the image `pvermeyden/nodejs-hello-world` on dockerhub and under the tags field, you will find the available tags for this image. Choose one of the tags and update the image field in your yaml. Once you have updated the image field with the correct tag, you may apply the changes using the following command (notice `oc create` command won't work here):
```bash
oc apply -f pod.yaml
```

You may also edit your pod directly using `oc edit`.
```bash
oc edit pod hello-world
```

