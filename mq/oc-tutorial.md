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
 ```
 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
 ```
 * (procedure) Run the following command to install the openshift-cli package:
 ```
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

### Create a test project on your OpenShift cluster
```
oc new-project test
```
