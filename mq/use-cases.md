# OCP Workshop Guide

## Overview
Red Hat OpenShift Container Platform (OCP) is a cloud-based Kubernetes platform for developing and running containerized applications. It is designed for ease-of-use and flexibility for both developers and end-users. OCP enables organizations to build, deploy, and scale applications quickly both on-premises and in the cloud. All while protecting your infrastructure with enterprise-grade security. This produces a more simple access point to underlying infrastructure that can help manage application life cycle and development workflows.

Welcome to day two of our workshop. Today we will be highlighting five features of OCP:
- Deploying a service
- Self-Healing
- Horizontal Pod Auto Scaling
- How to access the monitoring dashboard
- How to access log files

We will be using your environment that was prepared [earlier.](https://github.com/jmhossain/gitops-workshop-guide/blob/main/mq/README.md)

## Service Deployment from an Image

1. Navigate to your assigned IBM Cloud Environment. This will be listed in your spreadsheet under IBM Cluster URL. Afterwards, open the OpenShift Web Console in the upper righthand side of your Cluster.

![Cluster URL](https://user-images.githubusercontent.com/81570140/182544235-0e885a85-4090-4445-8525-9946e11bd705.png)

2. From the OCP Web Console, switch from `Administrator` to `Developer` view in the left dropdown menu.

![Swap to Developer mode](https://user-images.githubusercontent.com/81570140/182544822-dd8c431f-230b-40e0-94f6-08918c8be719.png)

3. Afterwards, click on `+Add` and select `Container images`.

![Add > Container Images](https://user-images.githubusercontent.com/81570140/182545138-42847d19-72f7-4c0d-8bb2-fe98890090b8.png)

4. Today for demononstration purposes, we will be deploying from a preloaded image from your environment's internal registry. This is to simulate deployment from your own private or public images. From the dropdowns labeled Project/Image Stream:Tag, select `tools` / `aaa`: `latest`

`aaa` is the name of the image for a test javascript application that we have created. This will be used later to demonstrate autoscaling. 

![Deploy Settings](https://user-images.githubusercontent.com/81570140/182545551-3121b732-828e-4834-af37-b5b2c85d86fb.png)

5. Default options will prepopulate. Take a glance at the options and settings available. Without changing the default settings, select `create`.

6. Switch back to `Administrator` view in the top left and select `Deployments` underneath `Workloads`. From here we can confirm that our new service `aaa` has been successfully deployed and is running successfully. Let's give this new service a little time to launch, and we'll return back to it for HorizontalPodAutoscaling.

![Service Deployed](https://user-images.githubusercontent.com/81570140/182547205-bde24052-49ab-4963-99ec-b3037206d825.png)

## Self-Healing
Let's take a glance at some of our other pods and see the tangible benefits of how OCP monitors and self heals.
1. Under `Workloads` select `Pods`. 

![Pods](https://user-images.githubusercontent.com/81570140/182551796-e0816b75-7164-4639-8bf8-267bab8c31c5.png)

2. Underneath the `Project:` dropdown menu, ensure `Tools` is selected.

![Tools](https://user-images.githubusercontent.com/81570140/182552138-48aa7189-73b6-42ef-9d8b-0a72d1f9265f.png)

3. In the search bar, type in `nav`.

![Nav](https://user-images.githubusercontent.com/81570140/182552415-7cd54c46-2ac8-4c50-87b3-b6835ec3d001.png)

4. Select the Owner of one of the running Pods for `integration-navigator`.

![Owner](https://user-images.githubusercontent.com/81570140/182552665-432b1e47-e506-47f5-9360-b1dce9f83a41.png)

5. Here we can view the `ReplicaSet` details that determine the current and desired states of our Pods.

![State](https://user-images.githubusercontent.com/81570140/182553049-6c331cc0-9922-4838-86bd-a6cc1f5a40e2.png)

6. Click on the `Pods` tab to view the running status of these Pods.

![Pods](https://user-images.githubusercontent.com/81570140/182553267-e8bfe7d8-4bab-4a6f-a74c-1b7e1de9bace.png)

Here we will simulate what would happen if someone 'accidentally' deleted one of these pods.

7. Take note of the last 5 characters of the names of these pods. Choose either one of them and select `Delete Pod` from the three dot drop down menu on the right hand side..

![Oopsie](https://user-images.githubusercontent.com/81570140/182553829-364aa113-a24d-4a8b-93c2-98d15cb9c54c.png)

8. Select `Delete` and pay close attention to what happens.

![Oh no!](https://user-images.githubusercontent.com/81570140/182554102-38e011a8-3366-4d40-83d2-8cc0adb35102.png)

Immediately you'll see that as that pod is terminated, a new one is created to replace it. You'll notice as well that the name of this new pod is different from the old one. This is because OpenShift is constantly monitoring and realizes that the current state (1 Pod) does not match the desired state (2 Pods).

![All good!](https://user-images.githubusercontent.com/81570140/182555184-6bdb81ca-5e4c-4b1e-979d-12fd4effc5c4.png)

But just like that, we've witnessed how OCP will automatically (based on user definitions) restore and self heal itself. And everything is back to normal!

## Horizontal Pod Auto Scaling

1. Navigate back to the service we deployed earlier under `Workloads` and `Deployments`. Left click `aaa` to view it.

![aaa!](https://user-images.githubusercontent.com/81570140/182557224-61b459ab-caaf-40dc-8b4a-b7ae763b0ee3.png)

2. Viewing the deployment details, you'll see that there is only 1 Pod is there and it's not using many resources currently. In the future, we believe this service will receive a lot of variable traffic. So let's simulate and prepare for this by creating a resource limit and add an Autoscaler.  Click on `Pods` to view the status of it.

![Status](https://user-images.githubusercontent.com/81570140/182557891-f86b1ebe-3df0-4fce-bdff-84118b69be02.png)

3. On the right hand side on the `Actions` dropdown, select `Edit resource limits`.

![Add Resource Limit](https://user-images.githubusercontent.com/81570140/182680772-e2989578-4b2b-443b-9393-e988272923bd.png)

4. Set the CPU Request to `600 milicores`.
 CPU Limit to `1 cores`.
 Memory Request to `500 Mi`.
 Memory Limit to `1000 Mi`.

![Resource Limits](https://user-images.githubusercontent.com/81570140/182681232-d4f9c160-e72c-4273-8efc-dfff643df4c9.png)

5. In the same Deployment, on the right hand side on the `Actions` dropdown, select `Add HorizontalPodAutoscaler`.

![image](https://user-images.githubusercontent.com/81570140/182682324-f4c75f0d-f254-4d89-a390-4ed15d589381.png)

6. Here we'll set the definitions for the Autoscaler itself. For the purposes of this workshop, let's set the `Minimum Pods` to `1` and `Maximum Pods` to `7`. Afterwards, set the `CPU Utilization` to `40` and the `Memory Utilization` to `60`. `Save` these settings.

![HPA Settings](https://user-images.githubusercontent.com/81570140/182683385-3c597809-71f6-4f2e-b16e-263df05c0e91.png)

Let's return back and view the Pods tab of our deployment. Looking back CPU Usage, not much is going on right now where we would currently need this to be autoscaled up yet. Let's take a look at some of the settings.

![CPU](https://user-images.githubusercontent.com/81570140/182558512-716b4424-f456-468e-ac28-ac9a77c618a0.png)

7. In the left menu, select `HorizontalPodAutoscalers` and left click `HPA example`.

![HPA](https://user-images.githubusercontent.com/81570140/182559452-e77bfc32-0ea2-42b3-9f66-75bf41f95463.png)

![image](https://user-images.githubusercontent.com/81570140/182559984-00c602ca-47fd-4d13-93fd-563511b88584.png)

Here you can view some of the user defined settings that we created for the autoscaler. These can naturally be editted in the YAML file as well.

Now let's prepare to simulate some traffic to test your autoscaler. I'll be using JMeter as a load testing tool to send HTTP Requests to your newly created `aaa` service. First let's retrieve the URL of `aaa`.

8. From the OCP Web Console, switch from `Administrator` to `Developer` view in the left dropdown menu and select `Topology`. Ensure that we are under the correct `Project:` named `tools`. 

![Topology](https://user-images.githubusercontent.com/81570140/182685309-169a1ee1-3b4a-4746-a526-746b19064d19.png)

9. Retrieve the URL from the `Open URL` icon in the upper right side of the node. The URL can also be found underneath the `Resources` tab underneath `Routes`.

![URL](https://user-images.githubusercontent.com/81570140/182686241-d7016195-ba3b-41fb-b92f-6370463ca7bb.png)

10. Send this URL to me in the chat and I'll simulate traffic to test your autoscaler! Return back to `Administrator` view, and view the deployment details of `aaa`.
You should see more pods are spun up depending on the traffic load received. As this happens you should see that more pods are created to distribute the load.

![Load Testing](https://user-images.githubusercontent.com/81570140/182687368-5712c85a-2292-4500-a07c-ee7a39150d8a.png)

11. After testing, return back in around ~15 minutes to confirm that these pods are scaled back down.

## Monitoring Dashboard

1. In the `Administrator` perspective in the OpenShift Container Platform web console, navigate to `Monitoring` → `Dashboards.`

2. Choose a dashboard in the Dashboard list. Some dashboards, such as etcd and Prometheus dashboards, produce additional sub-menus when selected.

3. Optional: Select a time range for the graphs in the Time Range list. Set a custom time range by selecting `Custom time range` in the `Time Range` list. Input or select the `From` and `To` dates and times. Click `Save` to save the custom time range.

![Dashboard](https://user-images.githubusercontent.com/81570140/182624429-051ff392-3dd1-4b61-a3af-0156e3baeb19.png)

4. Optional: Select a `Refresh Interval.`

5. Hover over each of the graphs within a dashboard to display detailed information about specific items.

There will be multiple menus and submenus to explore based on the metrics that OCP records.

## Accessing Log Files

1. In the `Administrator` perspective in the OpenShift Container Platform web console, navigate to `Workloads` → `Pods`.

2. In the `Project:` dropdown at the top, ensure `tools` is selected. Open one of the pods named `aaa` (there may be multiple).

![PodLogs](https://user-images.githubusercontent.com/81570140/182571500-af790e2c-ee90-430a-8d1a-e47f77ded9e1.png)

3. Select the `Logs` tab. Here, you may view the raw log file or download a copy.

![Logs](https://user-images.githubusercontent.com/81570140/182571208-aa0516bf-2d9d-4d38-975d-1179a236daff.png)

4. The webview is a much easier alternative to what previously would be done in CLI.

5. Return to your [Shell](https://cloud.ibm.com/shell) and log in with your OC Login Token.

6. Navigate to the proper project and copy the full name of the `aaa` pod.
```bash
oc project tools
```
```bash
oc get pods
```
![Get Pods](https://user-images.githubusercontent.com/81570140/182692162-82ad60b9-37d1-4340-a544-d42a5af7d8b6.png)

7. Replace the pod name in the command below and run the command.

```bash
oc logs aaa-useyourown-pod
```

Depending on whether or not that Pod is still running, you may receive a lot of output. But as you can see, OCP excels in simplifying the work processes shown today.
