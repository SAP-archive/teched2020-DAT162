# Exercise 2 - Data Masking

## Objective

This exercise will guide you through masking the account number in a table. We will then configure two users who will have different access rights to the masked data.

## Exercise Overview

- Configure data masking to hide parts of the account number
- Configure appropriate access roles to view masked and cleartext data

## Background

Dynamic data masking allows you to mask data at the table and view level in SAP HANA. While authorization is the primary means for access control, dynamic data masking provides an additional layer of access control. With this feature you can protect sensitive data by only displaying partial or obfuscated information.

This additional layer of protection allows you to hide data from power DBAs or other users who have broad access rights in the database. You can also use this feature to display or hide sensitive columns depending on a user’s role. One example of this would be if you have a call center where an agent only needs to reference the last four digits of a credit card number rather than the whole value, whereas the customer themselves or a supervisor might want access to the full credit card number. The native data masking feature in SAP HANA is dynamically applied during access, the original data stays unchanged.

In particular for our scenario, the *HR_SUPERVISOR* should see all the data, while the *HR_CALL_CENTER_AGENT* should not be able to see the full ACCOUNT_NO of an employee. The call center agent should not see the last three digits of it instead. We will create a SQL view that contains the required data masked, create two roles, one for the supervisors (that have to be able to see any data) and one for the call center agents (that only see the masked ACCOUNT_NO).

## Exercise 2.1 - Configure and Deploy Data Masking via a SQL View

As a very first step we will create the view including the data mask. We will create a file within the SAP Web IDE project that leads to the appropriate view on the SAP HANA cloud instance. Defining the view in the project is recommended, since it will be a portable unit that could be deployed to a different system if required.

1. Open the SAP Web IDE, navigate to the development perspective. You should see the example project files.
<br>![](/exercises/ex2/images/development_perspective.png)
2. Right click on the "db/src/" folder click on "New"->"File".
<br>![](/exercises/ex2/images/new_file.png)
3. Name the file "salaries_masked.hdbview" and click "OK".
<br>![](/exercises/ex2/images/new_salaries_masked_view.png)
4. The newly created file in the src folder will be opened in an editor. Enter the following SQL like DDL statement. The masking expression in the last line removes the last three digits of the ACCOUNT_NO.
```SQL
VIEW "SalarySQLDemo.db::SalariesMasked"("id", "firstname", "lastname", "masked_account_no", "salary", "start_year", "gender", "region", "zipcode", "T-Level", "education") 
AS SELECT "id",	"firstname", "lastname", "account_no", "salary", "start_year", "gender", "region", "zipcode", "T-Level", "education"  FROM "SalarySQLDemo.db::Salaries"
WITH MASK ("masked_account_no" USING LEFT("masked_account_no", 3) ||'***');
```
5. Save the file by typing "CTRL+S" or using the save button.

<br>![](/exercises/ex2/images/save_button.png)

6. Build (DB module, then project) and deploy the project, as described in the note at the end of (1.3)[/exercises/ex1#exercise-13-deploying-the-application-to-sap-hana-cloud].

7. Switch to the database explorer and open the newly created view, by selecting "Views" in the HDI container, right clicking on the "SalariesMasked" and choosing "Open Data".
<br>![](/exercises/ex2/images/open_view.png)
8. You will notice that the account number is masked, and the last three digits are invisible.
<br>![](/exercises/ex2/images/masked_view_data.png)

In the next step we need to define roles that allow the HR_SUPERVISOR as well as the HR_CALL_CENTER_AGENT to see either the masked or "unmasked" view.

## Exercise 2.2 - Configure Privileged and Non-Privileged Roles for Access

After completing this exercise, we will have created two roles, one for the supervisors and one for the call center agents. Then we will add the users *HR_SUPERVISOR* and *HR_CALL_CENTER_AGENT* to those roles. We will see the effects of the masking expression in example queries.

1. Roles are defined similar to views in files with the suffix .hdbrole in the project. Therefore, we add a new file by going back to the development perspective, right-clicking on the "db/src" folder and choosing "New"->"File".
<br>![](/exercises/ex2/images/new_file_role.png)

2. We name the first file "hr_supervisor_role.hdbrole" and press "OK". This will open the role editor in the WebIDE.
<br>![](/exercises/ex2/images/new_file_role_name.png)

3. **Important Note**: If the editor tells you after creating the file, the following message, please do the following to fix that: Edit the file with the editor as provided in Step (4), save this file, close the editor and re-open it. You might have to enter the role name again, and save it. Additionally, you could also use the code editor (right-clicking on the file, "Open in Code Editor") to double check the effects.

<br>![](/exercises/ex2/images/error_role_file.png)

4. We first need to edit the name. Note that entity names always have to have the prefix "SalarySQLDemo.db::" in order to belong to the correct namespace. In total you need to enter "SalarySQLDemo.db::HRSupervisorRole". **Note** if the error as described in Step (3) appears, please follow the instruction. So the result looks like this:
<br>![](/exercises/ex2/images/hr_supervisor_role_name.png)

5. In the center area of the screen we choose "Object Privileges" to define a role for an object (click on "+" to add a new privilege), choose the "SalarySQLDemo.db::SalariesMasked" object and add the "SELECT" as well as the "UNMASKED" privilege. 
<br>![](/exercises/ex2/images/design_role_hr_supervisor.png)

6. Save that file, either by pressing CTRL-S or by clicking the "Save" button.
<br>![](/exercises/ex2/images/save_button.png)

7. As a second role, we create a new file called "hr_call_center_agent_role.hdbrole" and configure it to only be able to select on the "SalariesMasked" view. We set the name to "SalarySQLDemo.db::HRCallCenterAgentRole". The configuration should look like this:
<br>![](/exercises/ex2/images/call_center_agent_role.png)

8. Save this role as well. Build (DB module, then project) and deploy the project, as described .in the note at the end of (1.3)[/exercises/ex1#exercise-13-deploying-the-application-to-sap-hana-cloud].

9. As a next step we need to assign the users we created in [Exercise 0](/exercises/ex0). In order to do so, we have to execute SQL commands in an *Admin* Console. Navigate to the Database Explorer, right click on the HDI container and choose "Open SQL Console (Admin)".
<br>![](/exercises/ex2/images/open_admin_sql_console.png)

10. In this console, we have to execute the following SQL Statements **one-by-one** in order to add the HR_SUPERVISOR user to the appropriate role. In a nutshell, three things are done here. First, a temporary table containing user and role information is created (line 1 and 2). Second, we call a SQL function that finally assigns the user to the role chosen (line 3) and third, drops the temporary table again (line 4).

```sql
CREATE LOCAL TEMPORARY COLUMN TABLE SALARYSQLDEMO#DI.#ROLES LIKE _SYS_DI.TT_SCHEMA_ROLES;
INSERT INTO SALARYSQLDEMO#DI.#ROLES ( ROLE_NAME, PRINCIPAL_SCHEMA_NAME, PRINCIPAL_NAME ) VALUES ( 'SalarySQLDemo.db::HRSupervisorRole', '','HR_SUPERVISOR' );
CALL SALARYSQLDEMO#DI.GRANT_CONTAINER_SCHEMA_ROLES( SALARYSQLDEMO#DI.#ROLES, _SYS_DI.T_NO_PARAMETERS, ?, ?, ?);
DROP TABLE SALARYSQLDEMO#DI.#ROLES;
```

11. The "CALL" statement should return messages similar to the following screenshot, please make sure that the granting message contains "ok" and no error.

<br>![](/exercises/ex2/images/grant_success.png)

12. To assign the HR_CALL_CENTER_AGENT to the appropriate role, we execute the following:

```sql
CREATE LOCAL TEMPORARY COLUMN TABLE SALARYSQLDEMO#DI.#ROLES LIKE _SYS_DI.TT_SCHEMA_ROLES;
INSERT INTO SALARYSQLDEMO#DI.#ROLES ( ROLE_NAME, PRINCIPAL_SCHEMA_NAME, PRINCIPAL_NAME ) VALUES ( 'SalarySQLDemo.db::HRCallCenterAgentRole', '','HR_CALL_CENTER_AGENT' );
CALL SALARYSQLDEMO#DI.GRANT_CONTAINER_SCHEMA_ROLES( SALARYSQLDEMO#DI.#ROLES, _SYS_DI.T_NO_PARAMETERS, ?, ?, ?);
DROP TABLE SALARYSQLDEMO#DI.#ROLES;
```

13. So both users are set now, and we can try it out. For that we have to create a new SQL console without the admin functionality. Right click on the HDI container and choose "Open SQL Console". .
<br>![](/exercises/ex2/images/open_sql_console.png)

14. In this new console, we have to switch users, e.g. to the HR_SUPERVISOR, this is possible with the following SQL command:

```sql
CONNECT HR_SUPERVISOR PASSWORD "putyourpasswordhere";
```
15. Querying now the SalariesMasked view with the HR_SUPERVISOR as a current user, returns in the full account number:
```sql
SELECT * FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesMasked";
```
<br>![](/exercises/ex2/images/salaries_unmasked.png)

16. Being the call center agent, does not reveal the complete ACCOUNT_NO.
```sql
CONNECT HR_CALL_CENTER_AGENT PASSWORD putyourpasswordhere;
SELECT * FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesMasked";
```
<br>![](/exercises/ex2/images/salaries_masked.png)

## Summary

In this exercise we have seen how specific parts of information can be hidden from a set of users. One needs to have a specific role to see the actual data.

Continue to - [Exercise 3](../ex3/README.md) to start learning why there is an anonymization feature, and how it works.
