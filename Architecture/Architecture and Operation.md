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

To minimize unnecessary traffic, static data will be stored (on-chain probably with *IPFS* or off-chain) under the responsibility of the *distributor*. The *distributor* will call the *xxList* or *xxInfo* service once to retrieve all available data (e.g., accommodations).

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

## Version compatibility

