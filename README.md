# HAPPEO

Assignment
Guidelines for the candidate

The goal of this assignment is to get an understanding of how you write code and your approach to problem solving. Do you write production ready code? Do not implement extra features. Think out of the box.

Please provide sufficient documentation in the readme.md file. Include your approach, problems that you faced, choices you made, etc. Also provide clear instructions to build, test, and run the project (make it easier to build and run, ahem.. docker).

## Technology choices

For the database, it is preferred that you use any embedded database (for example, H2), so there’s no need to spin up a database server).

We prefer the use of straightforward SQL queries and are not looking for the use of ORM tools.

## Submission

Submit your project as a zipped git repository (attach it in an email OR share a link to the zipped file uploaded on Google Drive, OneDrive, or any other cloud storage). Please preserve your commit history.

### Background: User Provisioning Feature

Your team develops user management for Happeo. One of the features is user provisioning. This means accepting users being pushed to the system by an external identity system.

### The external identity system

When a new user is created in the external identity system, it POSTs user data into the users endpoint of configured targets (similar to a webhook). The external identity system pushes users to the target system one at a time on an endpoint using a schema it defines.

The schema of the user entry as described by the external identity system is:

{

“email”: string,

“name”: {

“firstName”: string,

“lastName”: string,

}

“id”: string // id in external identity system

}

The external identity system expects the response in the following format:

When user is created

201 Created

{

“email”: string,

“name”: {

“firstName”: string,

“lastName”: string,

}

“id”: string // id in external identity system

“applicationId”: string // id in Happeo

}

For errors,

400 | 401 | 404 | 500

{

“error”: string

}

## Happeo

On Happeo’s side, we have decided that we will call systems that push users into Happeo as “provisioners”. We have decided that the endpoint on Happeo side will be,

/api/organisations/<ORG_ID>/provisioner/<PROV_ID>/users

where

PROV_ID is the id of the provisioner (the external identity system)

ORG_ID is the id of the Happeo organisation (i.e. Happeo’s customer - e.g. a company)

The users are essentially the employees of the company. Users access Happeo.

Happeo’s user database has the following schema

id - Happeo User’s internal autogenerated id

email - User’s email

organisation_id - Organisation ID of the user

first_name

last_name

external_id - ID of the user in the external system (null for internal user of Happeo)

password - Hashed password of the user (null for externally provisioned users)

is_active - User activation flag (true means user is active)

Implement the following stories:
Note - Assume that the Organisation ID you are working with is 1.

As an administrator of the organisation, I want to configure Happeo to create a new provisioner and generate a secret. This secret will be used by the external identity system to sign the JWT that gets sent along with the user entries. The provisioner ID should be auto generated.

Notes for the applicant - you are free to choose a REST API endpoint to expose here. Please document how to create a new provisioner and get the generated secret.

Accept Users pushed to the user provisioning endpoint (/api/organisations/<ORG_ID>/provisioner/<PROV_ID>/users) as per the schema provided by the external identity system (i.e. the provisioner, refer to the background section). When a user is created, it is inactive. The provisioner will send the request with a JWT in the Authorization header. This JWT is signed with a shared secret that was generated when the provisioner was created in Happeo.

As an administrator of the organisation, I want to list all inactive users and activate them. Additionally I would like to get users individually and activate them.

Notes for the applicant - you are free to choose a REST API endpoint to expose here (do not use the same endpoint in step 2). Please document how to retrieve the inactive users and activate them.

Do you see any security issues in this design? If yes, what could be your mitigation?

## Assumptions

For the actions made by the administrator, no need to implement authentication. Of course in the real world you would, but for this assignment let’s keep it simple.
