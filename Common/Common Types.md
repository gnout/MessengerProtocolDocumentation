[Back to ToC](https://github.com/gnout/MessengerProtocolDocumentation/blob/main/Documentation.md)

# Common Types

## Header

[Reference](https://buf.build/chain4travel/camino-messenger-protocol/docs/eea07d77749c4d28a05e0ac581fa3c7c:cmp.types.v1alpha#cmp.types.v1alpha.Header)

```protoBuf
message Header {
	// Protocol Version
	// The request and response should always hold the true version implemented.
	// This will help transparency and troubleshooting when issues arise. For example
	// in case a new filter is introduced with version 1.3.0 and a response by a supply
	// partner is still in version 1.1.0 that does not have the new filter. Obviously the
	// supply partner has not processed the new field and the response is not filtered as
	// specified in the request.
	Version version = 1;
	// End-user wallet ID for personalization purposes
	string end_user_wallet_address = 2;
}
```

A `Header` is used in all messages. In certain scenarios, this `Header` is included as part of a specific header construction. For example, there are two additional types: `RequestHeader` and `ResponseHeader`. Both types include the `Header` type.

The `Header` is essential for determining the message version. Whether receiving a request or response, the version must be checked for compatibility as described in [Version Compatibility](https://github.com/gnout/MessengerProtocolDocumentation/blob/main/Architecture/Architecture%20and%20Operation.md#version-compatibility)

### Version

```protoBuf
message Version {
	// API Version, major version which can have breaking changes (semantic
	// versioning)
	int32 major = 1;
	// API Version, minor version has backwards compatible new features (semantic
	// versioning)
	int32 minor = 2;
	// API Version, patch version small improvements and bugfixes (semantic
	// versioning)
	int32 patch = 3;
}
```

Comments in the `message` are very descriptive on what each number of the version is about

### Wallet Address

That's the end-user (traveler) wallet address. It can be used for personalization of the search results.