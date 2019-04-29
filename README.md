# GildedRose
(This document has been save at the main folder of GildedRose. It includes more figures that helps in manual testing.) 
Gilded Rose Take Home Assignment. 
Thank you for this interesting take-home assignment. I really enjoyed working on it. 
The first part of this document is to explain the approach, challenges, time effort, what has been accomplished in this API (version 1 and version 2), what are pending and can be available in the next version, and suggestions for future versions. The second part contains the original requirement with answers to the questions. 
This assignment looks simple at first glance, but like any coding assignment it can go through different versions with more functionalities, testing (manual, unit, or automated), refactoring, and other tasks. 
The first version of this API provides the basic Get verb that retrieves current inventory (“api/values/” and “api/values/{id}”) with no authentications. In addition, it provides “api/values/buy/{id}/{quantity}” with GET verb that returns a total price of the requested item (quantity * price). However, in Version 1, there is no tracking of item count because there is no ItemCount field in Item class. I tried to get a clarification about the question “Is it always possible to buy an item?”, but I haven’t received a reply on time. So, I decided to finish this API and save it as Version 1 without dealing with the quantity of items in the inventory. 
The effort to create version 1 API was around 4 hours that involves creating the solution, the item model, mocking a database, writing the codes, configuration in WebApiConfig, Basic Authentication with username and password for the “Buy” process with testing using Postman, and Unit Testing.
Then I created a new solution for version 2 for the same API. The GET command to retrieve current inventory is the same as it is in version 1. However, a new field ItemCount has been added to Item class to track the quantity of each item in the inventory. So, on each request to buy, the API is checking the existence of item as well as the available quantity. If the item exists and there is sufficient quantity, successful “Buy” request return total price and updates the item count by subtracting the requested quantity from ItemCount for the requested item in the database. (By the way Id has been added to Item Class in version 1 and 2 as well).
By adding ItemCount to Items class in version 2, I can answer the question “Is it always possible to buy an item?” and the answer is that it is only possible to buy in item if the item exists in the database and if the available item count is greater than or equal to the requested quantity. 
With the implemented changes in version 2, I thought that a mock database changes should persist between requests. Thus, it needed to be put in server cache to avoid it getting refreshed on each call and lose all added and bought items. So, HttpContext.Current has been used and it works fine. However, the challenge that I faced with this approach is in mocking cache in HttpContext in unit test. This can be done in version 3 if required. In addition, I added a POST verb to add a new item. This is not required but I added it for more testing. The effort for version 2 was an additional 4 hours. 
Finally, I merged the solutions for version 1 and version 2 in one project. So, in this case if version 1 satisfy the requirement of the assignment, you can use the following for testing: api/v1/values/, api/v1/values/1, api/v1/values/buy/1/4
Feel free also to check version 2 as well: api/v2/values/, api/v2/values/1, api/v2/values/buy/1/4

So, in total, the assignment with basic authentication consumed almost a full day (9 hours).
As for the user authentication I included a basic authentication which is not the ideal way for this API because access authorization for this API is authenticated on each call with username and password and we have no way to verify who is utilizing our Web API.
OAuth2 for authentication can be implemented in the next version of this API if required. Also, some more features can be included such as paging, sorting, filtering, and others. 

Below are the answers to the requirement.
The Gilded Rose Expands 
As you may know, the Gilded Rose* is a small inn in a prominent city that buys and sells only the finest items. The shopkeeper, Allison, is looking to expand by providing merchants in other cities with access to the shop's inventory via a public HTTP-accessible API.
API requirements
- Retrieve the current inventory (i.e. list of items)
Answer:
Endpoints: api/v1/values, api/v1/values/{id}, api/v2/values, api/v2/values/{id} 

- Buy an item (user must be authenticated)
Answer:
Endpoints: api/v1/values/Buy/{id}/{quantity}, api/v2/values/Buy/{id}/{quantity}
Example:
api/v2/values/Buy/1/4 where you are buying 4 quantities of item 1

Here is the definition for an item:

class Item
{
public string Name { get; set; }
public string Description { get; set; }
public int Price { get; set; }
}

The following changes have been implemented to Item Class:
-	Two version of Item Classes have been implemented: ItemV1 and ItemV2.
-	Id property was added to ItemV1 and ItemV2 classes as int type
-	ItemCount property was added to ItemV2 class as int type.

A couple questions to consider:
- How do we know if a user is authenticated?
Answer:
We know if the user is authenticated by returning the results which is total price. I have used very basic authentication. While buying an item, add two headers
username : test
password : test
value is same for both headers as it is defined in controller. 
Below is a sample request without headers or incorrect username/password results in “Authentication failure” response. It was used to understand how to process headers

 
Request with headers will return total amount in v1: (figure cannot be displayed in this file. Please check the Readme document in the main folder of the project)
 
Request with headers will return total amount and remaining quantity in v2:(figure cannot be displayed in this file. Please check the Readme document in the main folder of the project)
 
- Is it always possible to buy an item?
Answer:
In version1. it is possible to buy an item if the user provides an existing item Id and the user is authenticated.
In version 2, it is possible to buy an item if the user provides an existing item Id and the user is authenticated. In addition, it depends if the requested quantity is less than or equal existing item count in the inventory for the requested item.

Please model (and test!) accordingly. Using a real database is not required.
Two mocked databases (ItemV1Database and ItemV2Database) have been added in controllers.

Deliverables
1. A system that can process the two API requests via HTTP (done)
2. Appropriate tests (unit, integration, etc.)  (Please check the screen shots for manual testing. In addition, A Unit Test Project was added to the solution.)
Unit Tests image: (figure cannot be displayed in this file. Please check the Readme document in the main folder of the project)
 

3. A quick explanation of:
a. Choice of data format. Include one example of a request and response.
Answer:
Data is sent as parameter in get requests for both endpoints. Response is received in Json format. Please check the code in App_start/WebApiConfig.cs for JSON formatting. (figure cannot be displayed in this file. Please check the Readme document in the main folder of the project)

b. What authentication mechanism was chosen, and why 
Answer:
I have used basic authentication with username and password. This was chosen for the timing constraint of this project. It is not the ideal way of authentication. I will consider working on OAuth2 which is a token-based authorization scheme if asked because it is a more appropriate and efficient way for this API. With OAuth2, Access Authorization is authenticated only once based on the system’s existing user credential, then on successful authorization, an access token is provided for a specific period of time. The REST Web API call can utilize the access token which server will not authenticate again and again. In addition, the REST Web API call can track the user who is consuming the REST Web API. Moreover, OAuth2 is a better choice when the API is consumed run on cross platforms or consumed from different devices (laptop, mobile, ios, android, windows, …).
The following NuGet packages are required for the new version to add OAuth2. 
1.	Microsoft.Owin.Security.OAuth
2.	Microsoft.Owin.Cors
3.	Microsoft.AspNet.WebApi.Core
4.	Microsoft.AspNet.WebApi.Owin
One Small Reminder

The purpose of this exercise is for you to demonstrate your current skills, not pick up a new library/framework/workflow/IDE. We know developers can adapt, so for this exercise please use your preferred stack to deliver working and properly-tested code. 

The Assessment is to evaluate:
- Coding Style
- Use of framework/libraries
- Approach to development


Include unit test or some form of automated testing
If possible, include answers, notes/comments/design decision in a Readme.txt

This is your chance to show us what you consider to be great code (and tests!)
