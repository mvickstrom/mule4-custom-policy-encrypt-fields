# mule4-custom-policy-encrypt-fields
A custom policy for mule 4 which enrypts specific fields in a payload

This project demonstrates how to leverage Custom Policies in the MuleSoft Anypoint API Manager platform to secure information in an APIs payload such as Personally Identifiable Information (PII).

In this project are 2 APIs, 1 mule application and 2 custom policies. Below is an outline of each:

## encrypt-fields-policy
A custom policy in Mule 4 which has 2 input parameters, a list of 

## employees_api
A simple API which has one resource to get information about an Employee by their Id. The example querries a database and returns the first result which matches the Id URI parameter. The payload of this API has a social security number (ssn) field and a date of birth (dob) fields which represent the PII we are trying to prevent from being exposed unintentionally.

This API should have the encrypt-fields-policy applied to it in order to encrypt the ssn and dob fields so any consumers of the API will not by default see PII. 

## employeesync
Another simple API which querries the employees_api for an employee based on the Id URI parameter and then stores that employee record in a separate database table to represent an application which needs access to the un-encrypted PII data. 

In order to store the un-encrypted data from the employees_api we apply the decrypt-fields-policy to this API so it will unencrypt the specified fields. 

## Futher Improvements
The password used for encryption is stored as part of the configuration for the custom policy. Is stored encrypted in a protected database however when it is downloaded to the running API that configuration file for the policy is by default unnecrypted. I would suggest for production that you follow this article to encrypt that custom policy configuration file. https://docs.mulesoft.com/api-manager/2.x/api-gateway-encryption-mule4#custom-policy-encryption
