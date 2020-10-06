# Exercise 1 - Setting up the Web IDE Project and unerstanding the scenario

In this exercise, we will set up the Web IDE project, learn how to deploy example data and SQL entities to SAP HANA Cloud and introduce the scenario.

## Exercise 1.1 - Setting API Endpoint, Organization and Space

First, we need to set up the default API endpoint, Organization and Space, our application will be deployed to.

1. Log into your SAP Web IDE instance.
2. First, we need to configure the default Cloud Foundry endpoints. In order to do so, please navigate to the "Prefrences" Section on the left side of your screen (1) and select the "Cloud Foundry" entry in the "Workspace Preferences" (2) part. At (3) you can configure your API Endpoint, Organization and Space.
<br>![](/exercises/ex1/images/set_cf_api_endpoint.png)
3. Please choose an appropriate API Endpoint, e.g., if you are in Europe, you should choose https://api.cf.eu10.hana.ondemand.com. Once you selected one of those, you are asked for an E-mail and a Password. Please enter the credentials of your account you created in [Exercise 0](../ex0/README.md).
<br>![](/exercises/ex1/images/login_organization_space.png)
4. Please select the Organization and the Space as well. For a trial account the "Space" is always "dev" and the organization suffix is always "trial", it look similar like this:
<br>![](/exercises/ex1/images/chosen_api_endpoint_organization_space.png)
Please save the settings and proceed.

## Exercise 1.2 - Uploading the demo application

The demo application contains example data and prerequistes for the following exercises. In this step, we will upload this to the Web IDE.

1. Log into your SAP Web IDE instance.
2. Download the [SalarySQLDemo.zip](/exercises/ex1/downloads/SalarySQLDemo.zip) from this github repository.
3. Click on the "File" menu, choose "Import" -> "File or Project":
<br>![](/exercises/ex1/images/import_file_project.png)
4. Choose the downloaded [SalarySQLDemo.zip](/exercises/ex1/downloads/SalarySQLDemo.zip) from your disk:
<br>![](/exercises/ex1/images/chosen_file.png)
5. Uploading will take a bit, depending on your connection, once finished the folder structure should look like this: 
<br>![](/exercises/ex1/images/folder_structure.png)

We are now ready to deploy the example application to our SAP HANA Cloud instance.

## Exercise 1.3 - Deploying the application to SAP HANA Cloud

In this part of Exercise 1 we will build and deploy the example application to SAP HANA Cloud. Note that those steps will be required in the course of this whole lessons a couple of times and we will refer to it.

1. Right click on the main project "SalarySQLDemo" and choose "Build" -> "Build" from the menu. This will only create the basic structure and framework, we will have to re-do this step before deployment.
<br>![](/exercises/ex1/images/main_app_build.png)

2. After the build process is complete, the console in the middle says 'Build of "SalarySQLDemo" completed'
<br>![](/exercises/ex1/images/build_completed.png)

3. Now we have to build the "db" module, similar to the main application, right click on "db" and choose "Build"->"Build" from the menu.
<br>![](/exercises/ex1/images/build_db_module.png)

4. A popup might display asking you for credentials. Please enter your trial account credentials. **Note:** this might not be required if you are still logged into your cloud foundry account.
<br>![](/exercises/ex1/images/logon_for_build.png)

5. The build of the db module can take a while during the very first time. After the completion of that, build the main project as described in step 1 *again*. This will lead to the final creation of the complete "SalarySQLDemo_0.0.2.mtar".

6. Now we can finally deploy the .mtar, which is the last step of the process. Open the "mta_archives" folder in the project, right click on the "SalarySQLDemo_0.0.2.mtar" file and choose "Deploy" -> "Deploy to SAP Cloud Platform".
<br>![](/exercises/ex1/images/deploy_mtar.png)

7. You will be asked for a destination, while the endpoint chosen in Exercise 1.1 should be pre-chosen. Click "Deploy" to start the deployment.
<br>![](/exercises/ex1/images/final_deploy_endpoint.png)

8. Again, for the very first deployment, this might take a while it will get faster later. Once this is done, the console prints that:
<br>![](/exercises/ex1/images/deployment_successful.png)


**Important Note**: In the course of this exercise we will need to re-deploy the application. This means in particular, to:
1. Build the db module
2. Build the main project
3. Deploy the .mtar file created

So please memorize these steps for later.

## Exercise 1.4 Exploring the data 

After the succesful deployment of the demo project including demo data, we want to explore that data to get an idea of the scenario. During this process we will learn how to access the "Database Explorer" and execute SQL queries.

1. Navigate to the "Database Explorer" in your SAP Web IDE:
<br>![](/exercises/ex1/images/db_explorer.png)

2. Since we did not add any database yet, the system asks us to do so. We confirm that with "Yes":
<br>![](/exercises/ex1/images/add_new_db_dialogue.png)

3. Choose the "salary_demo_hdi_db" and press "Ok".
<br>![](/exercises/ex1/images/choose_hdi.png)

4. On the left side of your screen you will find a list of different database entities. 
<br>![](/exercises/ex1/images/demo_db.png)

5. Click on "Tables" and exactly one table should come up at the lower end of the screen:
<br>![](/exercises/ex1/images/tables.png)

6. Right click on it, and choose "Generate SELECT statement".
<br>![](/exercises/ex1/images/generate_select.png)

7. A SQL Console is opened, containing the following SQL statement:
```SQL
SELECT TOP 1000
	"id",
	"firstname",
	"lastname",
	"account_no",
	"salary",
	"start_year",
	"gender",
	"region",
	"zipcode",
	"T-Level",
	"education"
FROM "SALARYSQLDEMO"."SalarySQLDemo.db::Salaries"
```
8. Click the green arrow to run the statement, and you will see the results on the lower end of the screen.
<br>![](/exercises/ex1/images/run_sql_query.png)

9. With the help of the SQL console, you can execute arbitrary SQL queries, e.g., you could select all the male employees with:

```SQL
SELECT TOP 1000
	"id",
	"firstname",
	"lastname",
	"account_no",
	"salary",
	"start_year",
	"gender",
	"region",
	"zipcode",
	"T-Level",
	"education"
FROM "SALARYSQLDEMO"."SalarySQLDemo.db::Salaries"
WHERE "gender" = 'm';
```
## Exercise 1.5 - Understanding the demo scenario

In this exercise, we outline the scenario that is valid for the remaining part of this hands-on sesion: 

The HR department of a company holds their employee data in a database. This database contains a single table SALARIES with one record for each employee and the following fields:

1. ID
2. FIRST_NAME
3. LAST_NAME
4. ACCOUNT_NO
5. SALARY
6. START_YEAR
7. GENDER
8. REGION
9. ZIP_CODE
10. T_LEVEL
11. EDUCATION

The column names make the type of stored information obvious. From a privacy point of view, three different types of columns are present: 

1. Direct identifiers: ACCOUNT_NO is a uniquely identifying column for each person, FIRST_NAME and LAST_NAME in conjunction are also uniquely identifying. In order to anonymize a data set these values must not be released at all!
2. Sensitive values: SALARY is a sensitive value. In an anonymized data set, one should not be able to link its value to a single individual by means of background knowledge.
3. Quasi-identifiers: all remaining fields are quasi-identifiers. In conjunction (and together with background knowledge), their values may be used to link an entry in the database back to an individual, thus revealing the persons sensitve value or its membership in the database.

## Summary

We have now deployed the basic application and understood the data set. We will now continue on showing the masking feature and how it can help to satisfy the requirements of the HR supervisor and the call center agent.

Continue to - [Exercise 2 - Data Masking](../ex2/README.md)
