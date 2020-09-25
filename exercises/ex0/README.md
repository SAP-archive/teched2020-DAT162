# Getting Started

In this exercise you will learn how to prepare your environment for the actual part of this hands on session. Note that this session is based on trial accounts which are completely free of charge. We also recommend using (new) trial accounts to work in an isolated environment.

In particular we require to have trials of **SAP HANA Cloud** and **SAP Web IDE** (on Cloud Foundry). Furthermore, a trial account for the **SAP Cloud Platform** is the starting point for the product trials, and is required as well.

## Creating a SAP Cloud Platform Trial Account

In order to create a SAP Cloud Platform Trial Account, you have to conduct the following steps. This trial account is required to start the SAP HANA Cloud and SAP Web IDE trial.

1. Create an account on [sap.com](https://www.sap.com) or login.
<br>![](/exercises/ex0/images/sap_com_account.png)
2. With your sap.com account, log into the SAP Cloud Platform Trial to start it on https://account.hanatrial.ondemand.com/ and after login and verification, your screen should look like this:
<br>![](/exercises/ex0/images/start_sap_cp_trial.png)

This is only a quick description on the steps that have to be done to create an account. More detailed instructions can be found here: https://developers.sap.com/tutorials/hcp-create-trial-account.html.

## Creating a SAP HANA Cloud Trial Instance

After having your SAP Cloud Platform Trial Account ready, you should proceed creating a HANA Cloud Trial instance. In order to do so, please follow the following steps:

1. Enable the "Entitlements" required to start a SAP HANA Cloud instance. In order to do so, please follow this manual https://saphanajourney.com/hana-cloud/learning-article/starting-a-sap-hana-cloud-trial-when-you-have-an-existing-sap-cloud-platform-trial/

2. Create and start your SAP HANA Cloud Trial Instance as explained in this tutorial: https://saphanajourney.com/hana-cloud/learning-article/how-to-create-your-trial-sap-hana-cloud-instance/ Note that for this tutorial we do not have any special requirements other than the standard settings described in the step-by-step guide.

At the very end, your HANA Cloud instance should be running, as indicated by a green banner:

<br>![](/exercises/ex0/images/hana_cloud_running.png) 

After having created and started your SAP HANA Cloud Trial instance, continue with the SAP Web IDE trial.

## Creating a SAP Web IDE Trial Instance

Within your SAP Cloud Platform Trial, you have to subscribe to the SAP Web IDE as well for doing this hands-on session. Please refer to this guide, on how to do this: https://developers.sap.com/tutorials/webide-multi-cloud.html The very last step is to bookmark the Web IDE which we also recommend, since we will use the Web IDE intensively in the course of this session.

**Important Note:** While opening the SAP Web IDE there is a message stating that support for the WebIDE will end on November 13th. Please ignore this message.

<br>![](/exercises/ex0/images/webide_discontinued.png)

## Summary

We are now fully set and can start with the actual hands on exercise. As a very next step, we will upload the demo project, to have an environment with data.

Continue to - [Exercise 1 - Setting up the Web IDE Project](../ex1/README.md)