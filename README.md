# mule4-custom-policy-encrypt-fields
A custom policy for mule 4 which enrypts specific fields in a payload

This project demonstrates how to leverage Custom Policies in the MuleSoft Anypoint API Manager platform to secure information in an APIs payload such as Personally Identifiable Information (PII).

In this project are 2 APIs, 1 mule application and 2 custom policies. Below is an outline of each:

## encrypt-fields-policy
A custom policy in Mule 4 which will encrypt the value of the configured fields. The policy has 2 input parameters, a comma separated list of fields which should have their values encrypted and a password which is used to encrypt those values. In it's current implementation the policy expects a single object with the keys to be encrypted at the root level of the object. 

### Sample Input Payload 
```
{
  "userID": "2",
  "channel": "email",
  "ssn": "123-12-3123",
  "dob": "1978-03-04",
  "subscriberKey": "0033600001az9j9AAA",
  "emailAddress": "cthorborg@salesforce.com",
  "sentDate": "2020-05-17T17:45:00-03:00"
}
```

### Sample Encrypted Payload with "ssn, dob" fields selected
```
{
  "userID": "2",
  "channel": "email",
  "subscriberKey": "0033600001az9j9AAA",
  "emailAddress": "cthorborg@salesforce.com",
  "sentDate": "2020-05-17T17:45:00-03:00",
  "ssn": "yaEcVjU8m0VZxlhLBydHfQ==",
  "dob": "EBf+6ccOqELyDvx8ske4Kg=="
}
```

I followed [this](https://docs.mulesoft.com/api-manager/2.x/custom-policy-getting-started) guide to build out the archetype for the Mule 4 custom policy. 

## employees_api
A simple API which has one resource to get information about an Employee by their Id. The example querries a database and returns the first result which matches the Id URI parameter. The payload of this API has a social security number (ssn) field and a date of birth (dob) fields which represent the PII we are trying to prevent from being exposed unintentionally.

This API should have the encrypt-fields-policy applied to it in order to encrypt the ssn and dob fields so any consumers of the API will not by default see PII. 

## employeesync
Another simple API which querries the employees_api for an employee based on the Id URI parameter and then stores that employee record in a separate database table to represent an application which needs access to the un-encrypted PII data. 

In order to store the un-encrypted data from the employees_api we apply the decrypt-fields-policy to this API so it will unencrypt the specified fields. 

## encrypt-test
A sample application which was used to develop/test the encrypt/decrypt functionality. Building out the logic of a Mule custom policy in a sample Mule application first allows us to use the features of Anypoint Studio such as drag-and-drop, built in debugger and pallete to quickly develop the policy. Once the logic is working I simply copied the underly XML for the necessary components into the Maven archetype for my custom policy. Some modifications such as adding the XML namespace, removing the `doc` attributes, and copying over dependencies into the POM.xml file are required but this reduces the barrier to entry for developing a custom policy. 

## Futher Improvements
The password used for encryption is stored as part of the configuration for the custom policy. Is stored encrypted in a protected database however when it is downloaded to the running API that configuration file for the policy is by default unnecrypted. I would suggest for production that you follow [this](https://docs.mulesoft.com/api-manager/2.x/api-gateway-encryption-mule4#custom-policy-encryption) article to encrypt that custom policy configuration file. 
