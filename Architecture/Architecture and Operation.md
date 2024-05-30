[Back to ToC](https://github.com/gnout/MessengerProtocolDocumentation/blob/main/Documentation.md)

# Architecture and Operation

## Parties involved

The following two parties are involved in the operation of the plugin:

* *Distributor*: The party that initiates requests to obtain data
* *Supplier*: The party that receives requests and responds with the corresponding results

## Service types calls

There are three primary call types for each service:

* __xxList__: Retrieves a list of all available items with general information (e.g., a list of all accommodations).
* __xxInfo__: Retrieves comprehensive information for a specific item (e.g., all details about a particular accommodation).
* __xxSearch__: Searches the data with the intention of booking (e.g., booking an accommodation for specific dates).

## Static and Dynamic data

To minimize unnecessary traffic, static data will be stored off-chain under the responsibility of the *distributor*. The *distributor* will call the *xxList* or *xxInfo* service once to retrieve all available data (e.g., accommodations).

When the *distributor* performs an *xxSearch* with the intent to book, the supplier will return a result. The distributor will then match the `supplier_code` (explained below) from the search result with the corresponding code in the static data. 

__Important__: The *distributor* must handle data from different *suppliers* with care to ensure consistency.

### Supplier Code

This field must be consistent across all calls (*supplier's* responsibility) and serves as the link between the static and dynamic data. Every *xxList* call that the *supplier* returns must include the *supplier's* item ID (the `supplier_code`). Every *xxSearch* call that the *supplier* returns must include a consistent item ID (the `supplier_code`)

### Field `Timestamp last_modified`

Periodically, the *distributor* may call the *xxList* or *xxInfo* service again to retrieve updated data. The `last_modified` field will indicate the date and time of the *distributor's* most recent call. The *supplier's* response will include __only__ the updated data.  

Here is an example to illustrate the concept:  

* The *distributor* calls the *xxList* service on January 1st and receives 100 accommodations. 
* On February 1st, a new accommodation is added to the *supplier's* list. 
* The *distributor* calls the *xxList* service again and sets the `last_modified` value to January 1st (the date of the previous call).
* The *supplier* returns a result containing only the new accommodation added on February 1st.

This approach minimizes unnecessary data exchange, which is crucial for cost-efficiency on the blockchain.  

Every product contains a field called `ProductStatus`. The field is an enum and the definition is the following. When a *distributor* retrieves the updated list, each product will indicate its status. If the product is new, the status will be `PRODUCT_STATUS_NEW` if updated, `PRODUCT_STATUS_MODIFIED` if deleted, `PRODUCT_STATUS_DEACTIVATED`. This informs the *distributor* about product creation, updates, or deletions.

```protoBuf
enum ProductStatus {
	PRODUCT_STATUS_UNSPECIFIED = 0;
	PRODUCT_STATUS_NEW = 1;
	PRODUCT_STATUS_MODIFIED = 2;
	PRODUCT_STATUS_DEACTIVATED = 3;
}
```

## Version compatibility

Message exchange between a *distributor* and a *supplier* requires compatible versions of the Messenger Protocol to avoid potential incompatibilities. Otherwise, message incompatibility may occur.  

Every message within the protocol includes a field of type `Version` (see "Common Types" for details). This type consists of three numbers: major version, minor version, and patch.  

In essence, when a *distributor* sends a request message, the *supplier* must respond with a message using the same major version.

Here's how it works: The `Version` type implements semantic versioning for the Protocol. Semantic versioning comprises three numbers that form a version:

* major version: Introduces significant changes, potentially including breaking changes from previous versions.
* minor version: Introduces minor changes and new features that are backward compatible.
* patch: Includes bug fixes and other improvements.

When sending a request, the *distributor* must include the Protocol version used by its plugin. Upon receiving the request, the *supplier* must check the requested Protocol version. If the *supplier* can handle a compatible message (same major version), it sends a response with the same major version as the request.

This ensures compatibility between Protocol versions used by *distributor* and *supplier* plugins.

__Note__ While minor version differences between *distributor* and *supplier* plugins won't disrupt communication, functionality may be impacted. However, a minor version may include for instance a new item in an enum which may refine a search. Although, the protocol will not break, the search results may not utilize the new enum item and could be incompatible with the intended search parameters. *Distributors* and *suppliers* must check request and response versions, keeping potential compatibility issues in mind. 
