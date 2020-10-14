# Exercise 3 - Data Anonymization

## Objective

This exercise will explain, how you can anonymize data in a way that the privacy of individuals is preserved while you are still able to gain valuable analytical results based on that data. But fist, we will clarify why data masking as learned in the exercise before, is not enough for this goal.

## Exercise Overview

- Explore how personal records can be re-identified
- Learn how to use anonymization methods in SAP HANA
- Evaluate analytical queries on anonymized data

## Background

Assume you are in the shoes of a Data Scientist at ACME and you want to analyze if there is a dependency between the year an employee started in the company, and the average salary. We would assume the longer somenone works at a certain place the higher the salary on an average level. In order to test this hypothesis, the data scientist needs access to HR data but must not reveal an individual salary. You might think that the easiest solution is to remove the name and the account number from the data set with Data Masking, but in fact it is not.

Although Data Masking can be a great tool to reduce the information exposed from sensitive data, for analytical use cases it is often not enough. Masking generally only obscures or hides a specific column entry as we did in the previous exercise. By combining different data points and having some prior knowledge, it is often possible to  re-identify certain individuals even if data is masked. We will show an example in the first part of this exercise.

In order to anonymize the data set, there are three methods supported by SAP HANA data anonymization (also on premise starting from SAP HANA 2.0 SPS04). k-Anonymity is one of the three supported. You can think of it as hiding individuals in a crowd. This method is a structured approach to generalize properties of individuals to hide them in larger groups. The k in k-Anonymity stands for the minimum number of individuals in a group.

If applied properly, situations where individuals can be singled out do not happen: individuals appeared with unique combinations of attributes that reidentified them. The first step to apply k-Anonymity is removing direct identifiers. These are someone’s name or account number and the name. To prevent someone from being re-identified based on unique combinations of attributes you need to generalize such quasi identifier values. These can be age, gender, zip-code, education level, etc.. By configuring a sufficient k value and properly applying the method, it will no longer be possible to easily identify a single person by its uniqueness. Determining the right value of k (and other parameters) for a specific scenario usually involves discussions with your Data Protection and Privacy Officer (DPPO).

## Exercise 3.1 - How can data be re-identified?

In this exercise we will do a re-identification showing that we don't need a name to identify a record from the data set.

1. Switch to the database explorer of your SAP WebIDE and create a new SQL console.
<br>![](/exercises/ex3/images/open_sql_console.png)

2. Assume that we do know a male colleague, that started in 1993, lives in the area of ZIP code 7203, did a Bachelor and is currently in the T-Level T5. Based on this information, we execute the following query:

```sql
SELECT * FROM  "SalarySQLDemo.db::Salaries"
WHERE "start_year" = 1993 and "gender" = 'm' and "zipcode"= 7203 and "education" = 'Bachelor' and "T-Level" = 'T5';
```

3. There is exactly one row returned, which means we found the salary record of the colleague named *Donald* and without actually using his name or any other direct identifier we could find out his salary, which is a severy privacy issue.

<br>![](/exercises/ex3/images/salary_donald.png)

4. You might say, that this is only an effect for this specific person, however this happens to a number of individuals, since the different attributes lead to a large dimensional space.

```sql
SELECT "start_year", "gender", "region", "zipcode", "T-Level", "education", count(*), MAX("salary") FROM "SalarySQLDemo.db::Salaries" 
GROUP BY "start_year","gender","region","zipcode","T-Level","education" 
HAVING count(*)=1;
```

5. Note that we have more than 1000 rows returned, it is just cut because of the database explorer preferences.

<br>![](/exercises/ex3/images/all_reid.png)

In turn, obviously achieving both - preserving the privacy, and allowing data scientists to gain information is not achievable by removing names and identifiers such as the account number. For that we need anonymization methods.

## Exercise 3.2 - Applying k-Anonymity

In this exercise we will learn how k-Anonymity helps to protect the privacy of individuals when thirds access the data sets. Reconsidering the challenges we have seen in the last exercise, the idea of k-Anonymity is to hide individuals like *Donald* in a crowd. Assume that a query for a specific start year, education, gender, zipcode and T-Level result in a number of rows, one cannot re-link any of those rows to a specific individual. This is what k-Anonymity does, and the minimum result set size of any combination of those attributes is k or larger.

Anonymization methods are applied by creating a so called anonymized view, which we will do in the exercise for the salary table.

1. Open the development perspective, and create a new file in the "db/src" folder.
<br>![](/exercises/ex3/images/new_file_view.png)

2. We name it "salaries_kanonymity.hdbview".
<br>![](/exercises/ex3/images/new_view.png)

3. In the newly created file, we add this SQL like statement. In a nutshell it is the definition of a privacy view. It defines that 8 rows/persons are indistinguishable and chooses the algorithm k-Anonymity. For each quasi-identifying column we add a hierarchy (which is written explicitly in the statement) that allows the system to generalize values until at least 8 persons are indistinguishable. **Note:** There are several ways of defining hierarchies. For the sake of this demo here, we have chosen the "embedded" variant of the hierarchies, which require a rather long definition in the CREATE statement.

```sql
VIEW "SalarySQLDemo.db::SalariesKAnonymity" ("id", "start_year", "gender", "region", "zipcode", "T-Level", "education", "salary") AS
SELECT "id", "start_year", "gender", "region", "zipcode", "T-Level", "education", "salary"
FROM "SalarySQLDemo.db::Salaries"
WITH ANONYMIZATION (ALGORITHM 'K-ANONYMITY' PARAMETERS '{"k": 8}'
COLUMN "id" PARAMETERS '{"is_sequence":true}'
COLUMN "gender" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["f"],["m"]]}}'
COLUMN "region" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["APJ"],["EMEA"],["NA"]]}}'
COLUMN "T-Level" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["T1","T1-T2"],["T2","T1-T2"],["T3","T3-T5"],["T4","T3-T5"],["T5","T3-T5"]]}}'
COLUMN "education" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["9th-12th","High School","Secondary Ed"],["Assoc-acdm","Professional Ed","Higher Ed"],["Assoc-voc","Professional Ed","Higher Ed"],["Bachelor","Undergraduate","Higher Ed"],["College","Undergraduate","Higher Ed"],["Doctorate","Graduate","Higher Ed"],["HS-grad","High School","Secondary Ed"],["Master","Graduate","Higher Ed"],["Prof-school","Professional Ed","Higher Ed"]]}}'
COLUMN "zipcode" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["5004","50xx"],["5005","50xx"],["5006","50xx"],["5104","51xx"],["5105","51xx"],["5106","51xx"],["5204","52xx"],["5205","52xx"],["5206","52xx"],["6006","60xx"],["6007","60xx"],["6008","60xx"],["6106","61xx"],["6107","61xx"],["6108","61xx"],["6206","62xx"],["6207","62xx"],["6208","62xx"],["7002","70xx"],["7003","70xx"],["7004","70xx"],["7102","71xx"],["7103","71xx"],["7104","71xx"],["7202","72xx"],["7203","72xx"],["7204","72xx"]]}}'
COLUMN "start_year" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["1987","1986-1990","1986-1995"],["1988","1986-1990","1986-1995"],["1989","1986-1990","1986-1995"],["1990","1986-1990","1986-1995"],["1991","1991-1995","1986-1995"],["1992","1991-1995","1986-1995"],["1993","1991-1995","1986-1995"],["1994","1991-1995","1986-1995"],["1995","1991-1995","1986-1995"],["1996","1996-2000","1996-2005"],["1997","1996-2000","1996-2005"],["1998","1996-2000","1996-2005"],["1999","1996-2000","1996-2005"],["2000","1996-2000","1996-2005"],["2001","2001-2005","1996-2005"],["2002","2001-2005","1996-2005"],["2003","2001-2005","1996-2005"],["2004","2001-2005","1996-2005"],["2005","2001-2005","1996-2005"],["2006","2006-2010",">2006"],["2007","2006-2010",">2006"],["2008","2006-2010",">2006"],["2009","2006-2010",">2006"],["2010","2006-2010",">2006"],["2011","2011-2015",">2006"],["2012","2011-2015",">2006"],["2013","2011-2015",">2006"],["2014","2011-2015",">2006"],["2015","2011-2015",">2006"],["2016","2016-2020",">2006"],["2017","2016-2020",">2006"]]}}');
```

4. Save the file, build and deploy the application.

5. Open the Database Explorer, and check the views in your HDI container. You should find the newly created one.

<br>![](/exercises/ex3/images/k-anonymity-view-available.png)

6. In order to be able to use the k-anonymity view, we need to REFRESH it first (this is a one time operation per view). Open the SQL console and enter the following query:

```sql
REFRESH VIEW "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity" ANONYMIZATION;
```

7. The result should be a successful execution message.

<br>![](/exercises/ex3/images/refresh_view.png)

8. **Note:** The following steps in Exercise 3.2 are optional and show how to improve the hierarchy design. If you want to continue with the normal exercise, go to [Exercise 3.4](/exercises/ex3/README.md#exercise-33-showing-the-effects-of-k-anonymity). Motivation of this additional part is, that defining embedded hierarchies is a rather cumbersome process for columns with many different values. We will create a SQL Script funtion for the ZIP Code Hierarchy, that simplifies the process. Switch back to the development perspective and add a new file in the db/src folder.

<br>![](/exercises/ex3/images/create_new_file_function.png)

9. Name this file "hierarchyfunction.hdbfunction"

<br>![](/exercises/ex3/images/file_hierarchyfunction.png)

10. Insert the following code into the new file and save. This function reduces the number of digits in Zip Codes, and replaces the missing numbers with "*".

```sql
FUNCTION "SalarySQLDemo.db::HIERARCHYFUNCTION"(value nvarchar(255), level integer)
RETURNS outValue nvarchar(255) AS
BEGIN
  DECLARE charsToShow integer default 0;
    charsToShow := length(value) - level;
    outValue := lpad(substring(value, 1+level), charsToShow+level,'*');
END;
```

11. Next, open the "salaries_kanonymity.hdbview" file and insert the following SQL like statement and save the file. Note that for the column "zipcode" the newly created function is referenced. This makes the definition far more versatile.

```sql
VIEW "SalarySQLDemo.db::SalariesKAnonymity" ("id", "start_year", "gender", "region", "zipcode", "T-Level", "education", "salary") AS
SELECT "id", "start_year", "gender", "region", "zipcode", "T-Level", "education", "salary"
FROM "SalarySQLDemo.db::Salaries"
WITH ANONYMIZATION (ALGORITHM 'K-ANONYMITY' PARAMETERS '{"k": 8}'
COLUMN "id" PARAMETERS '{"is_sequence":true}'
COLUMN "gender" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["f"],["m"]]}}'
COLUMN "region" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["APJ"],["EMEA"],["NA"]]}}'
COLUMN "T-Level" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["T1","T1-T2"],["T2","T1-T2"],["T3","T3-T5"],["T4","T3-T5"],["T5","T3-T5"]]}}'
COLUMN "education" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["9th-12th","High School","Secondary Ed"],["Assoc-acdm","Professional Ed","Higher Ed"],["Assoc-voc","Professional Ed","Higher Ed"],["Bachelor","Undergraduate","Higher Ed"],["College","Undergraduate","Higher Ed"],["Doctorate","Graduate","Higher Ed"],["HS-grad","High School","Secondary Ed"],["Master","Graduate","Higher Ed"],["Prof-school","Professional Ed","Higher Ed"]]}}'
COLUMN "zipcode" PARAMETERS '{"is_quasi_identifier":true, "min": 1, "hierarchy":{"function":"SalarySQLDemo.db::HIERARCHYFUNCTION", "levels": 3}}'
COLUMN "start_year" PARAMETERS '{"is_quasi_identifier":true,"hierarchy":{"embedded":[["1987","1986-1990","1986-1995"],["1988","1986-1990","1986-1995"],["1989","1986-1990","1986-1995"],["1990","1986-1990","1986-1995"],["1991","1991-1995","1986-1995"],["1992","1991-1995","1986-1995"],["1993","1991-1995","1986-1995"],["1994","1991-1995","1986-1995"],["1995","1991-1995","1986-1995"],["1996","1996-2000","1996-2005"],["1997","1996-2000","1996-2005"],["1998","1996-2000","1996-2005"],["1999","1996-2000","1996-2005"],["2000","1996-2000","1996-2005"],["2001","2001-2005","1996-2005"],["2002","2001-2005","1996-2005"],["2003","2001-2005","1996-2005"],["2004","2001-2005","1996-2005"],["2005","2001-2005","1996-2005"],["2006","2006-2010",">2006"],["2007","2006-2010",">2006"],["2008","2006-2010",">2006"],["2009","2006-2010",">2006"],["2010","2006-2010",">2006"],["2011","2011-2015",">2006"],["2012","2011-2015",">2006"],["2013","2011-2015",">2006"],["2014","2011-2015",">2006"],["2015","2011-2015",">2006"],["2016","2016-2020",">2006"],["2017","2016-2020",">2006"]]}}');
```

12. Build and deploy the project.

13. We need to refresh the view again, repeat steps 5-7 in this exercise, i.e., execute the REFRESH statement in the database explorer.

```sql
REFRESH VIEW "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity" ANONYMIZATION;
```

The anonymized view is now ready to use and we will do so in the next part of this exercise.

## Exercise 3.3 - Showing the effects of k-Anonymity

After we created the view, we will have a look at the results. 

1. Open a new SQL console at the database explorer.

<br>![](/exercises/ex3/images/open_sql_console.png)

2. We first try to get an idea how the data looks like now, so we execute the following SQL statement.

```sql
SELECT * FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity";
```

3.  We can see that k-Anonymity chose to group the start year in 5 year buckets, shortened the ZIP code to two digits and grouping the T-Level in buckets as well.

<br>![](/exercises/ex3/images/select_results.png)

4. As a second exercise we check if at least *k* persons are indistinguishable. With the following query, we search for rows with a combination of start_year, gender, region, zipcode, T-Level and education of less than eight rows. Execute the following query in the SQL console:

```sql
SELECT "start_year", "gender", "region", "zipcode", "T-Level", "education", count(*), MAX("salary") FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity"
GROUP BY "start_year","gender","region","zipcode","T-Level","education" HAVING count(*)<8;
```

5. The result of this query, is an empty result set, which we expect, since at least *k* rows should be indistinguishable.

<br>![](/exercises/ex3/images/zero_rows_returned.png)

6. As a last check, we calculate the number of indistinguishable persons that are there at least, and we expect this number to be at least 8. Execute the following query:

```sql
SELECT "start_year", "gender", "region", "zipcode", "T-Level", "education", count(*) as cnt, MAX("salary") FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity" 
GROUP BY "start_year","gender","region","zipcode","T-Level","education" 
ORDER BY cnt;
```

7. The results look as expected, at least 8 persons are in each group.

<br>![](/exercises/ex3/images/indistinguishable_persons.png)

In this exercise we have seen how to apply k-Anonymity and what the effects are. The data is now anonymous and can be used for analytical purposes without risking the privacy of individuals.

## Exercise 3.4 - Evaluating a data science task on anonymized data

This exercise is about using the anonymized data for an actual evaluation task. In order to do so, we create a new role that lets the data scientists access the anonymized view, but not the original data, and then execute the actual analysis on the data.

1. Switch back to the development perspective and add a new file in the db/src folder.

<br>![](/exercises/ex3/images/create_new_file_role.png)

2. Name the new file data_scientist_role.hdbrole and click "ok"

<br>![](/exercises/ex3/images/create_new_file_role_name.png)

3. Configure the role: name it SalarySQLDemo.db::DataScientistRole and add the SELECT privilege for the object SalarySQLDemo.db::SalariesKAnonymity. Note that you have to choose "Object Privileges" in the menu, to add a privilege to the view.

<br>![](/exercises/ex3/images/ds_role_editor.png)

4. Save the file, build and deploy the project.

5. Switch back to the database explorer and open an SQL Console (**Admin**).

<br>![](/exercises/ex3/images/open_sql_console_admin.png)

6. We now add the role to the data scientist user, by executing this SQL step-by-step:

```sql
CREATE LOCAL TEMPORARY COLUMN TABLE SALARYSQLDEMO#DI.#ROLES LIKE _SYS_DI.TT_SCHEMA_ROLES;
INSERT INTO SALARYSQLDEMO#DI.#ROLES ( ROLE_NAME, PRINCIPAL_SCHEMA_NAME, PRINCIPAL_NAME ) VALUES ( 'SalarySQLDemo.db::DataScientistRole', '','DATA_SCIENTIST' );
CALL SALARYSQLDEMO#DI.GRANT_CONTAINER_SCHEMA_ROLES( SALARYSQLDEMO#DI.#ROLES, _SYS_DI.T_NO_PARAMETERS, ?, ?, ?);
DROP TABLE SALARYSQLDEMO#DI.#ROLES;
```

7. Now the user DATA_SCIENTIST has access to the anonymized data, but not the original one. In order to test this, please open a SQL Console.

<br>![](/exercises/ex3/images/open_sql_console.png)

8. Execute this statement to switch to the DATA_SCIENTIST user:

```sql
CONNECT DATA_SCIENTIST PASSWORD passwordhere;
```

9. And then execute a SELECT on the anonymous view:

```sql
SELECT * FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity";
```

10. Querying the original table results in an error, which is of course expected as well.

```sql
SELECT * FROM "SALARYSQLDEMO"."SalarySQLDemo.db::Salaries";
```
<br>![](/exercises/ex3/images/privilege_error.png)

11. So the privileges are configured correctly, and we can start querying for our analytical purpose in answering the question if being longer at ACME improves the salary in average. The following query reflects this information need, execute that in a SQL Console.

```sql
SELECT "start_year", ROUND(AVG("salary"),2) FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesKAnonymity" GROUP BY "start_year" ORDER BY "start_year";
```

12. The results show clearly that the longer you are at ACME the higher the average salary is. Due to the k-Anonymization we see only five year ranges of start years and not the exact start year, however the average salary of that cohort is precise.

<br>![](/exercises/ex3/images/kanon_salary_startyear.png)

## Exercise 3.5 - Applying Differential Privacy

A different way of anonymizing data in SAP HANA is Differential Privacy. In contrast to k-Anonymity, the goal of Differential Privacy is not to prevent re-identification, but to hide sensitive information by applying random noise. In contrast to Data Masking, Differential Privacy still imposes privacy guarantees. Follow this exercise to learn how to apply Differential Privacy.

1. Switch to the development perspective right-click on the "db/src" folder and add a new file.

<br>![](/exercises/ex3/images/create_new_file_role.png)

2. Name this file salaries_differentialprivacy.hdbview.

<br>![](/exercises/ex3/images/new_view_dp.png)

3. The editor of that file opens, add this SQL like statement into this editor and save the file. You will notice that only the sensitive column "salary" is configured. There are two parameters we have to configure: "epsilon" and "sensitivity". In short, the higher epsilon, the lower the privacy guarantee and the sensitivity should reflect the range of the column.

```sql
VIEW "SalarySQLDemo.db::SalariesDifferentialPrivacy" ("id", "start_year", "gender", "region", "zipcode", "T-Level", "education", "salary") AS
SELECT "id", "start_year", "gender", "region", "zipcode", "T-Level", "education", "salary"
FROM "SalarySQLDemo.db::Salaries"
WITH ANONYMIZATION (ALGORITHM 'DIFFERENTIAL_PRIVACY' PARAMETERS ''
COLUMN "id" PARAMETERS '{"is_sequence":true}'
COLUMN "salary" PARAMETERS '{"is_sensitive":true, "epsilon" : 0.5, "sensitivity": 100000}');
```

4. Build the project.

5. Before we query the results, we need to modify the Data Scientist role, to add a SELECT privilege for the newly created view. Open the "data_scientist_role.hdbrole" and add a second object privilege as select for the Differential Privacy view. Open the "data_scientist_role.hdbrole" file and add a second Object Privilege (click the "+") assigning SELECT privileges for the SalarySQLDemo.db::SalariesDifferentialPrivacy object.

<br>![](/exercises/ex3/images/add_dp_view_to_role.png)

6. Build the project and deploy it.

7. Now switch to the Database Explorer and open a SQL console.

<br>![](/exercises/ex3/images/open_sql_console.png)

8. As the next step we need to REFRESH the Differential Privacy view to initialize it. Do this by executing the following query in the SQL console:

```sql
REFRESH VIEW "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesDifferentialPrivacy" ANONYMIZATION
```

9. After having done this, we can switch to the DATA_SCIENTIST user by executing:

```sql
CONNECT DATA_SCIENTIST PASSWORD passwordhere;
```

10. We can now query the SalariesDifferentialPrivacy view, to see the effects of differential privacy, by executing:

```sql
SELECT * FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesDifferentialPrivacy";
```
<br>![](/exercises/ex3/images/results_differential_privacy.png)

11. You will notice that the salaries are far off the original values, in particular there are also some negative values in the dataset. This is the effect of Differential Privacy. Each row alone does not make sense any more with respect to the salary. However, aggregating data still results in meaningful values. In order to see that, we execute the same analytical query as for the k-Anonymous view on the Differential Private one as well:

```sql
SELECT "start_year", ROUND(AVG("salary"),2) FROM "SALARYSQLDEMO"."SalarySQLDemo.db::SalariesDifferentialPrivacy" GROUP BY "start_year" ORDER BY "start_year";
```

<br>![](/exercises/ex3/images/avg_startyear_dp.png)

12. The result shows the trend as expected and the trend is equal to the k-Anonymous result. Even though any salary value was changed by far, the average over the whole data set still returns meaningful results. This time, we get the exact start year, but the average salary will be a bit off the original value.

That summarizes the Differential Privacy part of this exercise. You have learned how to deploy a differential private view.

## Summary

We have seen in this exercise that it is possible to anonymize a data set and still conduct meaningful results. Anonymization can be done with different methods having different impact on privacy and utility. Anonymization can be configured in views that return always the latest data from the original table and do not require any additional maintenance once deployed.

Continue to - [Exercise 4](../ex4/README.md) to start learning how anonymization reports can help the data protection and privacy officer.