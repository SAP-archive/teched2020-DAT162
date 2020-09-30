# DAT162 - Get the Most from Sensitive Data: SAP HANA Data Anonymization and Masking

## Description

This repository contains the material for the SAP TechEd 2020 session called DAT162 - Get the Most from Sensitive Data: SAP HANA data anonymization and masking. 

## Overview

This session introduces attendees to the SAP HANA data masking and SAP HANA data anonymization functionality, by the running example of the company ACME. We will use SAP HANA Cloud, SAP HANA Cockpit and the SAP Web IDE. We will show how these features help to get the most of the data, without risking the privacy of individuals. This includes also 

Our example company ACME has, like any other company, a database of its employees containing amongst other salaries and address information. There are multiple stakeholder personas within ACME that require access to the data, ranging from productive users, to data scientists:
- **HR Supervisor**: Has to be able to see and maintain any HR data.
- **HR Call Center agent**: Is of course allowed to see some details, e.g., when an employee calls and asks if the address is still correct. However, some information like the account number of an employee should be hidden that this information cannot be leaked.
- **Data Scientist**: This persona requires access to analyze the HR data, e.g., how the payment structure of employees is. Of course, some data is required, but individual salaries must not be revealed.

Throughout this exercise we will use this as a running example.


## Requirements

The requirements to follow the exercises in this repository are:
- SAP HANA Cloud
- SAP Web IDE
Both are available as a trial, and this tutorial will focus on the trial version that it is accessible to anyone.

Instructions on how to start and use such trials are in [Exercise 0](exercises/ex0/).

## Exercises

- [Exercise 0 - Setting up the environment](exercises/ex0/)
    - [Exercise 0.1 - Creating a SAP Cloud Platform Trial Account](exercises/ex0#exercise-01---creating-a-sap-cloud-platform-trial-account)
    - [Exercise 0.2 - Creating a SAP HANA Cloud Trial Instance](exercises/ex0#exercise-02---creating-a-sap-hana-cloud-trial-instance)
    - [Exercise 0.3 - Creating a SAP Web IDE Trial Instance](exercises/ex0#exercise-03---creating-a-sap-web-ide-trial-account)
    - [Exercise 0.4 - Creating the required users](exercises/ex0#exercise-04---creating-the-required-users)
- [Exercise 1 - First Exercise Description](exercises/ex1/)
    - [Exercise 1.1 - Exercise 1 Sub Exercise 1 Description](exercises/ex1#exercise-11-sub-exercise-1-description)
    - [Exercise 1.2 - Exercise 1 Sub Exercise 2 Description](exercises/ex1#exercise-12-sub-exercise-2-description)
- [Exercise 2 - Second Exercise Description](exercises/ex2/)
    - [Exercise 2.1 - Exercise 2 Sub Exercise 1 Description](exercises/ex2#exercise-21-sub-exercise-1-description)
    - [Exercise 2.2 - Exercise 2 Sub Exercise 2 Description](exercises/ex2#exercise-22-sub-exercise-2-description)


**IMPORTANT**

Your repo must contain the .reuse and LICENSES folder and the License section below. DO NOT REMOVE the section or folders/files. Also, remove all unused template assets(images, folders, etc) from the exercises folder. 

## How to obtain support

Support for the content in this repository is available during the actual time of the online session for which this content has been designed. Otherwise, you may request support via the [Issues](../../issues) tab.

## License
Copyright (c) 2020 SAP SE or an SAP affiliate company. All rights reserved. This file is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
