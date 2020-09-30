# Exercise 0 - Setting up the environment

In this exercise you will learn how to prepare your environment for the actual part of this hands on session. Note that this session is based on trial accounts which are completely free of charge. We also recommend using (new) trial accounts to work in an isolated environment.

In particular we require to have trials of **SAP HANA Cloud** and **SAP Web IDE** (on Cloud Foundry). Furthermore, a trial account for the **SAP Cloud Platform** is the starting point for the product trials, and is required as well.

## Exercise 0.1 - Creating a SAP Cloud Platform Trial Account

In order to create a SAP Cloud Platform Trial Account, you have to conduct the following steps. This trial account is required to start the SAP HANA Cloud and SAP Web IDE trial.

1. Create an account on [sap.com](https://www.sap.com) or login.
<br>![](/exercises/ex0/images/sap_com_account.png)
2. With your sap.com account, log into the SAP Cloud Platform Trial to start it on https://account.hanatrial.ondemand.com/ and after login and verification, your screen should look like this:
<br>![](/exercises/ex0/images/start_sap_cp_trial.png)

This is only a quick description on the steps that have to be done to create an account. More detailed instructions can be found here: https://developers.sap.com/tutorials/hcp-create-trial-account.html.

## Exercise 0.2 - Creating a SAP HANA Cloud Trial Instance

After having your SAP Cloud Platform Trial Account ready, you should proceed creating a HANA Cloud Trial instance. Please make sure you remember the password you will set for the DBADMIN user, since we will need this later. In order to do so, please follow the following steps:

1. Enable the "Entitlements" required to start a SAP HANA Cloud instance. In order to do so, please follow this manual https://saphanajourney.com/hana-cloud/learning-article/starting-a-sap-hana-cloud-trial-when-you-have-an-existing-sap-cloud-platform-trial/

2. Create and start your SAP HANA Cloud Trial Instance as explained in this tutorial: https://saphanajourney.com/hana-cloud/learning-article/how-to-create-your-trial-sap-hana-cloud-instance/ **Note**: For this tutorial we do not require that you allow all external IP addresses to access your SAP HANA Cloud instance. In particular, in step 4, please choose the option as shown here:
<br>![](/exercises/ex0/images/hana_cloud_set_ip_to_all.png)
 This is necessary to allow the Web IDE to access your instance during deployment. For a productive setup, you might consider limiting the IP address ranges, but in order to make it simple for this tutorial, we allow all. This setting can also be changed after instance creation, i.e., limits can also be applied afterwards.

At the very end, your HANA Cloud instance should be running, as indicated by a green banner:

<br>![](/exercises/ex0/images/hana_cloud_running.png)

After having created and started your SAP HANA Cloud Trial instance, continue with the SAP Web IDE trial.

## Exercise 0.3 - Creating a SAP Web IDE Trial Instance

Within your SAP Cloud Platform Trial, you have to subscribe to the SAP Web IDE as well for doing this hands-on session. Please refer to this guide, on how to do this: https://developers.sap.com/tutorials/webide-multi-cloud.html The very last step is to bookmark the Web IDE which we also recommend, since we will use the Web IDE intensively in the course of this session.

**Important Note:** While opening the SAP Web IDE there is a message stating that support for the WebIDE will end on November 13th. Please ignore this message.

<br>![](/exercises/ex0/images/webide_discontinued.png)

## Exercise 0.4 - Creating the required users

Throughout this whole exercise we require three SAP HANA users that represent the personas as outlined. In order to have them ready once required, we create them now. We will create the user *HR_SUPERVISOR*, *HR_CALL_CENTER_AGENT* and *DATA_SCIENTIST*

1. Navigate to your SAP HANA Cloud trial tile, and open the SAP HANA Cockpit
<br>![](/exercises/ex0/images/open_hana_cockpit.png)
2. As the new browser windows open, you might be asked for database credentials. Please enter the credentials of the *DBADMIN* user including the password set during creating the trial instance.
3. Navigate to the "Security and User Management" in the database overview.
<br>![](/exercises/ex0/images/security_user_management.png)
4. Open "User Management"
<br>![](/exercises/ex0/images/user_management.png)
5. In order to create a new user, click the "+" in the Users section and choose "Create User":
<br>![](/exercises/ex0/images/add_user1.png)
6. Fill out the form to create a new user, in particular, set the username to *HR_SUPERVISOR*, set a password and **important** do not forget to check "No" for forcing the user to set a new password after the first logon. Please remember the password, we will need that later.
<br>![](/exercises/ex0/images/add_user2.png)
7. Once done with the form, click the "Save" button to create the user.
<br>![](/exercises/ex0/images/add_user3.png)
8. Please repeat those steps to create the *HR_CALL_CENTER_AGENT* and the *DATA_SCIENTIST* users.

## Summary

We are now fully set and can start with the actual hands on exercise. As a very next step, we will upload the demo project, to have an environment with data.

Continue to - [Exercise 1 - Setting up the Web IDE Project](../ex1/README.md)
