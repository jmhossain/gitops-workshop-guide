# IAM fix

Sometimes users can not see their clusters in IBM Cloud web dashboard. Run the following commands from the cloud.ibm.com/shell:

1. Log in IBM Cloud via CLI:
```
ibmcloud login --sso
```
*Note that answering `Y` to the question "Open the URL in the default browser? [Y/n]", may not work in the cloud.ibm.com/shell and you may have to copy the link into your broswer.*

2. Select the account `ITZ - SQUAD (5ac779bd279b4301adc82d45bea4fd85) <-> 2071092`.

3. Switch to `itzroks` resource group.
```
ibmcloud target -g itzroks
```

4. List clusters:
```
ibmcloud ks cluster ls
```

Done. User should see his/her cluster in CLI and Web.
