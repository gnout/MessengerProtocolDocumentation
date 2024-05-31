[Back to ToC](https://github.com/gnout/MessengerProtocolDocumentation/blob/main/Documentation.md)

# Accommodation Product List Request

* [Messenger Protocol Reference](https://buf.build/chain4travel/camino-messenger-protocol/docs/main:cmp.services.accommodation.v1alpha#cmp.services.accommodation.v1alpha.AccommodationProductListService)
* [ProtoBuf](https://buf.build/chain4travel/camino-messenger-protocol/file/main:cmp/services/accommodation/v1alpha/list.proto#L29)
* [Open Message Diagram](https://storage.googleapis.com/docs-cmp-files/diagrams/c4t/proto/cmp/services/accommodation/v1alpha/list.proto.dot.svg)

The *Accommodation Product List* service is part of the [static data](https://github.com/gnout/MessengerProtocolDocumentation/blob/main/Architecture/Architecture%20and%20Operation.md#static-and-dynamic-data) architecture. Therefore, the *distributor* should call this service infrequently to minimize unnecessary traffic and store the results under its own responsibility.

## ProtoBuf

```protoBuf
message AccommodationProductListRequest {
  // Message header
  cmp.types.v1alpha.RequestHeader header = 1;
  // Only respond with the products that are modified after this timestamp
  google.protobuf.Timestamp modified_after = 2;
}
```

## Example 1

```java
{
  "header": {
    "version" {
        "major": 1,
        "minor": 12,
        "patch": 0
    },
    "end_user_wallet_address": "0x71C7656EC7ab88b098defB751B7401B5f6d8976F",
  },
  "modifiedAfter": "2024-01-01T00:00:00Z" 
}
```

### From Distributor perspective

By sending this request, the *distributor* indicates the following intentions:

* The *distributor* sets the version number of its messenger plugin to 1.12.0 and expects a compatible response.
* the `end_user_wallet_address` should be ignored, as this service is used for data storage by the *distributor*, not for personalization purposes by traveler wallets. Personalization by the traveler's wallet is not necessary (otherwise the *distributor* will get and store data only for a particular wallet)
* By specifying the `modifiedAfter` field, the *distributor* requests only accommodations added, updated, or deleted after the specified date.   

### From Supplier perspective

Upon receiving this request, the *supplier* must consider the following:

* A response compatible with the *distributor's* plugin version must be compiled. Otherwise, the *distributor's* plugin may not understand the response. If unable to compile a compatible response, the *supplier* may send a warning/error message, or respond and let the *distributor* decide what to do with the response.
* The `end_user_wallet_address` should be ignored as this call is for static data handling (not particular to an end user)
* The *supplier* must honor the `modifiedAfter` date and return accommodations added, updated, or deleted after that date.

## Example 2

```java
{
  "header": {
    "version" {
        "major": 1,
        "minor": 12,
        "patch": 0
    },
    "end_user_wallet_address": "0x71C7656EC7ab88b098defB751B7401B5f6d8976F",
  },
  "modifiedAfter": "" 
}
```

`modifiedAfter` is not defined

### From Distributor perspective

The *distributor* signifies the intent to get all the available accommodations. 

### From Supplier perspective

The *supplier* should return all the available accommodations
