# payeezy-samples
payeezy-samples contains two projects
* `payeezy-client` employs best practices using the Spring Framework to call the [Payeezy Credit Card Transactions API](https://developer.payeezy.com/creditcardpayment/apis/post/transactions) and the [Payeezy Capture or Reverse a Transaction](https://developer.payeezy.com/capturereversepayment/apis/post/transactions/%7Bid%7D)
* `credit-card-transactions` is a web-based application using Spring MVC to demonstrate calling the `payeezy-client`

## `payeezy-samples` with Spring Boot
These samples have been developed using Spring Boot. Spring Boot makes it easy to create stand-alone, production-grade Spring based Applications that you can "just run". Boot takes an opinionated view of the Spring platform and third-party libraries so you can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration.

The primary goals of Spring Boot are:
* To provide a radically faster and widely accessible ‘getting started’ experience for all Spring development
* To be opinionated out of the box, but get out of the way quickly as requirements start to diverge from the defaults
* To provide a range of non-functional features that are common to large classes of projects (e.g. embedded servers, security, metrics, health checks, externalized configuration)

Spring Boot does not generate code and there is absolutely no requirement for XML configuration.

A Quick Start Guide to Spring Boot is available [here](http://projects.spring.io/spring-boot/)

To call the Payeezy API, you will need to [register and create a new user account with the Payeezy Developer Portal](https://developer.payeezy.com/user/register). Registering with the portal, gives you credentials to call the Payeezy API. The credentials include
* API Secret
* API Key
* Merchant Token
* Merchant Id

## Types of Payeezy Requests implemented
These samples describe calling two types of Payeezy requests

1. Credit Card Payments like an `AUTHORIZE` or `PURCHASE`

2. Capture and Reverse a payment like `VOID`, or `REFUND`. This is a two step process where a primary transaction like `AUTHORIZE` OR `PURCHASE` is called before making a Secondary Transaction like `VOID` or `REFUND`.

## Make a Credit Card Payments request using the Payeezy API
To make a Credit Card Payments request (Primary Transaction) using the Payeezy API, 

1. Create a new Credentials object `new Credentials(key, secret, token);`
2. Create a PayeezyClient `new PayeezyClient(new Credentials(key, secret, token), url);`. The url for credit card transactions is https://api-cert.payeezy.com/v1/transactions
3. Create a `TransactionRequest`
4. Call `payeezyClient.post(transactionRequest)`

For an example, please see `purchaseTransaction` [here](https://github.com/nadkau-pivotal/payeezy-samples/blob/master/payeezy-client/src/test/java/io/pivotal/payeezy/PayeezyClientTests.java)

## To Capture and Reverse a Payment
To Capture and Reverse a payment made using Credit Card Payments API
1. Make a Primary Transaction as described above
2. If the Primary Transaction succeeds, call `post` again, with a `TransactionRequest` and the `TransactionId` from the Primary Transaction `payeezyClient.post(transactionRequest, id);`

For an example, please see `voidTransaction` [here](https://github.com/nadkau-pivotal/payeezy-samples/blob/master/payeezy-client/src/test/java/io/pivotal/payeezy/PayeezyClientTests.java)

## credit-card-transactions
To build your payeezy application, you may choose to leverage a cloud native platform like Pivotal Cloud Foundry (PCF) to avail of a host of services like MySQL, RabbitMQ, Redis etc. `credit-card-transactions` uses Spring Boot and calls the ClearDB MySQL Database service on the Pivotal's public deployment of PCF, called Pivotal Web Services (PWS). A sample deployment of the `credit-card-transactions` app is available [here](http://credit-card-transactions.cfapps.io/)
### Spring Boot, Cloud Foundry and Data Services
To Capture and Reverse Payment requests, this application first issues a Cardit Card Payment request (Primary Transaction); stores the salient values from the `ResponseEntity` of the Primary Transaction into a database; and finally, retrieves the values from the database to issue a Secondary Transaction.
#### Look Ma, No `Datasource`!
Notice, however, that the **code does not define a `DataSource` at all**. When run locally, if `H2` is on the classpath, Spring Boot creates the `H2` embedded `Datasource` for you.
When deployed to a Cloud Native Platform like Pivotal Cloud Foundry, the Cloud Foundry buildpack will detect a database service binding and create a `DataSource` for you. If you add Spring Cloud Connectors as well, your app will also work in other cloud platforms, as long as you include a connector.

To run this application,
1. Git clone this repository
2. Run `mvn package`
3. Run `java -jar target/credit-card-payments-0.0.1-SNAPSHOT.jar`.
Alternatively, you may deploy the jar to a Cloud Native Platform like Pivotal Cloud Foundry by issuing a `cf push`. 
For a detailed tutorial on deploying to Pivotal Cloud Foundry, please see the [Developer Guide](http://docs.run.pivotal.io/devguide/deploy-apps/deploy-app.html) in the Pivotal Web Services Documentation.

## Deploy to Pivotal Cloud Foundry 

### Setup Your PWS Account 
Pivotal Cloud Foundry allows you to deploy your applications either on a private virtualized environment like OpenStack or VMware's vSphere and vCloud Air or a public deployment like AWS. Pivotal's public deployment of PCF on AWS is called Pivotal Web Services (PWS). You can sign up for a 60 day free trial of PWS at [run.pivotal.io](www,run.pivotal.io). When you create a PWS account, you are provided with credentials to push, monitor and manage applications on PWS.

### Download, Install and Set Up the CF cli 
Cloud Foundry provides a command-line tool called CF cli to seamlessly push your applications to Pivotal Cloud Foundry. More information on downloading and installing the CF cli tool can be found [here](http://docs.run.pivotal.io/starting/#install-login). Once you've downloaded and installed the CF cli, you may login to PWS using the CF cli. Logging into PWS targets it for deployment of your applications.

## Deploy Your Application to Pivotal Cloud Foundry 
Once you have downloaded and installed the CF cli, you are ready to push applications to Pivotal Cloud Foundry. 
To push applications to Pivotal Cloud Foundry, run:

`cf push APP-NAME`

`cf push` provides several deployment options. To view these options, run:

`cf push --help`

You may also choose to set deployment parameters in a manifest file. To deploy applications with a manifest file, please see the section titled "Deploying with Application Manifests" [here](http://docs.run.pivotal.io/devguide/deploy-apps/manifest.html). A detailed tutorial on deploying applications to Pivotal Cloud Foundry is available [here](http://docs.run.pivotal.io/devguide/deploy-apps/deploy-app.html).


