# Exercise 2 - Data Masking

## Objective

This exercise will guide you through masking the account number in a table. We will then configure two users who will have different access rights to the masked data.

## Exercise Description

- Configure Data Masking to hide parts of the account number
- Configure appropriate access roles to view masked and cleartext data

## Background

Dynamic Data Masking allows you to mask data at the table and view level in SAP HANA. While Authorization is the primary means for access control, dynamic Data Masking provides an additional layer of access control. With this feature you can protect sensitive data by only displaying partial or obfuscated information.

This additional layer of protection allows you to hide data from power DBAs or other users who have broad access rights in the database. You can also use this feature to display or hide sensitive columns depending on a user’s role. One example of this would be if you have a call center where an agent only needs to reference the last four digits of a credit card number rather than the whole value, whereas the customer themselves or a supervisor might want access to the full credit card number. The native Data Masking feature in SAP HANA is dynamically applied during access, the original data stays unchanged.


## Exercise 2.1 Configure and Deploy Data Masking via a SQL View




## Exercise 2.2 Configure Privileged and Non-Privileged Roles for Access

After completing these steps you will have...

1.	Enter this code.
```abap
DATA(lt_params) = request->get_form_fields(  ).
READ TABLE lt_params REFERENCE INTO DATA(lr_params) WITH KEY name = 'cmd'.
  IF sy-subrc = 0.
    response->set_status( i_code = 200
                     i_reason = 'Everything is fine').
    RETURN.
  ENDIF.

```

2.	Click here.
<br>![](/exercises/ex2/images/02_02_0010.png)

## Summary

You've now ...

Continue to - [Exercise 3 - Excercise 3 ](../ex3/README.md)
