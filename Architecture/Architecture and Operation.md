[Back to ToC](https://github.com/gnout/MessengerProtocolDocumentation/blob/main/Documentation.md)

# Architecture and Operation

## Parties involved

Following are 2 parties involved in the operation of the plugin:

* *Distributor*: is the party that makes the request to get data (i.e. HotelPlan)
* *Supplier*: is the party that receives the request and responds with the results (AVRA)

## Service types calls

There are 3 major calls for every service

* xxList: A list will get all the available items with general information (i.e. all the accommodations) 
* xxInfo: All available information for a specific item (i.e. all the information that we have about an accommodation)
* xxSearch: Search the data with the intent to book (i.e. book an accommodation for specific dates)

## Static and Dynamic data

In order to avoid unnecessary traffic, static data will be stored (on chain or off chain) with the responsibility of the *distributor*. The *distributor* will call once the *xxList* service to get all the available information (i.e. accommodations)  

When the *distributor* will perform a *xxSearch* with the intent to book, our web API will return a result and the *distributor* will match the __supplier_code__ (description follows) returned by the search with the one in the static data

### Supplier Code

This field need to be consistent in all calls (our responsibility) and is the link between the static data and the dynamic data. Every *xxlist* that we return should have our item id (the __supplier_code__). Every *xxSearch* that we return should have consistent item id ((the __supplier_code__))

### Timestamp last_modified

Every now and then, the *distributor* may call the *xxList* service to get an updated list. The __last_modified__ field will contain the items that were updated or created after that date. For example, the *distributor* may call the accommodation list again to find out which accommodation have been added or updated after his last call (..and add them to his static data)  

As AVRA we don't this information. We will resolve this internally without involving the plugin. If we have this information, the documentation will be updated accordingly.

## Getting data from our API

The mapping between the the protocol and our web API is not always 1 on 1.  

In some case multiple calls to our web API need to be made in order to gather the necessary data. In this case the documentation will include all the calls that are needed to complete the necessary data

In some other cases, the protocol will ask information for multiple products where our web API can only return information for only 1 product. Also in this case multiple calls to our api are necessary to bring data for all the requested products

## Usernames and passwords

In our web API, users (username and password) are connected to a specific client. The responses of our web API will differ depending on "who is asking!". For the plugin to work properly we will need to have a mapping between the *distributor* and the credentials that belongs to the particular distributor so that the results of the web API are correct.
