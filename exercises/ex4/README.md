# Exercise 4 - Data Anonymization Report

## Objective

In this exercise we will show how a data protection officer can gain information on anonymized views from the SAP HANA Cockpit.

## Exercise Overview

- Access the SAP HANA Cockpit
- Learn how to access the Anonymization repots

## Background

After having created anonymized views, one last persona in ACME having requirements remains: the data protection and privacy officer (DPPO). The DPPO is responsible for making sure personal data is processed according to regulations, e.g., anonymized in the right way. We have already learned that anonymization can be configured in several different ways, so being the in the shoes of a DPPO a report is benificial to keep track and check anonymizations. Since metadata on anonymization is stored in SAP HANA we can create such reports, and they can be found in the SAP HANA Cockpit.

## Exercise 4.1 - Accessing the anonymization reports

For this exercise we need to navigate to the SAP HANA Cockpit and locate the anonymization reports. **Note:** if you have the SAP HANA Cockpit still open from Exercise 0, you can skip step 1-3.

1. Navigate to https://account.hanatrial.ondemand.com and enter your trial account
<br>![](/exercises/ex4/images/account_hanatrial.png)

2. In your trial account, navigate to the dev space and find your SAP HANA Cloud instances. Click on "Open In" and select "SAP HANA Cockpit"
<br>![](/exercises/ex4/images/open_hana_cockpit.png)

3. The SAP HANA cockpit might ask for credentials, use the DBADMIN user you created during the start phase of the trial.

4. Once you are logged in, it is important to understand that the SAP HANA Cockpit is organized in "groups" containing different tiles for administration. The "Anonymization Report" will be under "Security and User Management", but this is not necessarily the screen you will have right from the start. In order to have the most general way, we navigate to the "All" Section (containing all the tiles) to find the "Anonymization Report" tile.

<br>![](/exercises/ex4/images/navigate_to_all.png)

5. Type in the search field "anon" and the respective tile will show up. Click on "View available anonymization views"

<br>![](/exercises/ex4/images/find_anon_report_tile.png)

6. We created two different anonymized views, and both show up in the list. This is the place where a DBADMIN or a data protection officer with a different account can see all the anonymized views. In the next exercise we will go into details of those reports. Note that there will be multiple reports, some with the Status READY and some only with the Status CREATED. The "ready" ones are the interesting ones, the others just artifacts from the build process and cannot be used by a data consumer.

<br>![](/exercises/ex4/images/anon_reports.png)

## Exercise 4.2 - Understanding the report

1. We start with the report on the k-Anonymity view, so we click on "SALARYSQLDEMO:SalarySQLDemo.db::SalariesKAnonymity" and the actual report comes up.

<br>![](/exercises/ex4/images/choosing_k_anonymous_report.png)

2. The anonymization report itself has three areas, area (1) is covering the anonymization method itself, area (2) is covering so called "KPIs" and area (3) is reflecting the schema and possible quasi identifiers with the respective hierarchies. We will explain more details below the screenshot, please note that behind the most numbers and names, explanations on it can be accessed by clicking on the links, e.g., click on "k-anonymity" to receive an explanation direct in the report.

<br>![](/exercises/ex4/images/k_anonymity_report.png)

- *Section 1:* Contains the chosen anonymization method and the parameters, e.g., k for it. In this case we choose k-Anonymity and a k of 8.

- *Section 2:* KPIs are reflecting on the performance in terms of utility of the anonymization method. Note that *privacy* guarantees are kept anyways, KPIs reflect on the actual effects of such guarantees. For example, in the column "Model Risk" there is the "Inverse k" which reflects the chosen k value. The larger those groups are, the less likely it is that we link a single record by chance back to an individual, i.e., 1/k. However, groups might be larger than k, since the modifications can lead to the situation that the smallest group contains more than k individuals. This is reflected in the "Inverse k Effective" which is always smaller or equal than the "Inverse k". More details on the KPIs can be found in the documentation and by clicking on the actual values to open a popup.

- *Section 3:* This section contains information on the affected columns, in particular the columns marked as quasi-identifying for k-Anonymity. The actual hierarchy can be seen by clicking on the "Yes" in the right most columns. Note that the DPPO cannot see any data but metadata.

3. As a next step we want to look at the differential privacy report, in order to access this, click on the go back button, to return to the overview of anonymized views.

<br>![](/exercises/ex4/images/go_back_button.png)

4. Choose the differentially private view.

<br>![](/exercises/ex4/images/dp_anon_view.png)

5. The report has obviously different fields and information, e.g., the epsilon is displayed, but it has a very similar structure to the report we looked into in detail.

<br>![](/exercises/ex4/images/dp_report.png)

6. As a very last step, we would like to mention the possibility to actually download a text based representation of this report by clicking in the upper right corner on the "Download" link. This can be used to archive reports and this is of course available for each anonymization method.

## Summary

We have seen in this exercise that it is possible to make the anonymization views transparent to a DPPO. This is important, since the DPPO has to stay informed.