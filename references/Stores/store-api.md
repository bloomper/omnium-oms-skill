# API Reference

Source: https://docs.omnium.no/docs/Stores/store-api

> Learn all you need to know about the store API

## Omnium Store API

The **Omnium Store API** allows you to manage and retrieve store-related data, including store details, opening hours, and more. It provides several operations to create, retrieve, update, and delete store records, making it easy to integrate with external systems. The API enables seamless management of store locations and configurations, as well as the ability to manage special opening hours for holidays and other exceptions.

Each store contains essential details such as location, contact information, operating hours, and more. With this API, you can efficiently manage and customize store data for smooth operations across your system.

* * *

## Store Model

A store in Omnium is represented by a JSON object with the following structure:

```json
{
  "id": "Acme",
  "name": "acme.com",
  "address": {
    "streetName": "Street name",
    "streetNumber": "1",
    "city": "New York",
    "zipcode": "10001",
    "region": "NY",
    "regionCode": "",
    "country": "USA",
    "countryCode": "US"
  },
  "availableOnMarkets": ["USA"],
  "phoneNumber": "212-111-111-1111",
  "email": "acme@omnium.com",
  "comment": null,
  "url": "",
  "latitude": "40.7237",
  "longitude": "-73.9898",
  "googlePlaceId": "",
  "mondayOpeningHours": {
    "isOpen": true,
    "openFrom": "08:00:00",
    "openTo": "16:00:00"
  },
  "additionalOpeningHours": [
    {
      "date": "2019-04-19T00:00:00",
      "openingHours": {
        "isOpen": false,
        "openFrom": "00:00:00",
        "openTo": "00:00:00"
      },
      "closed": true
    }
  ],
  "externalIds": null,
  "isWarehouse": true,
  "isPrimaryWarehouse": true,
  "isPublicVisible": false,
  "properties": null
}
```

This structure includes the following fields:

*   **id**: Unique store identifier.
*   **name**: Store name or domain name.
*   **address**: The store’s physical address.
*   **availableOnMarkets**: Markets where the store is available.
*   **contact information**: Phone number and email.
*   **opening hours**: Regular and special hours for each day of the week.

You can find more details in the [Store model documentation](https://api.omnium.no/documentation/index.html#/model-OmniumStore).

* * *

## Store Example

A webshop located at Manhattan, New York City, New York, US could look like this:

```json
{
  "id": "Acme",
  "name": "acme.com",
  "address": {
    "streetName": "Street name",
    "streetNumber": "1",
    "city": "New York",
    "zipcode": "10001",
    "region": "NY",
    "regionCode": "",
    "country": "USA",
    "countryCode": "US"
  },
  "availableOnMarkets": ["USA"],
  "phoneNumber": "212-111-111-1111",
  "email": "acme@omnium.com",
  "comment": null,
  "url": "",
  "latitude": "40.7237",
  "longitude": "-73.9898",
  "googlePlaceId": "",
  "mondayOpeningHours": {
    "isOpen": true,
    "openFrom": "08:00:00",
    "openTo": "16:00:00"
  },
  "additionalOpeningHours": [
    {
      "date": "2019-04-19T00:00:00",
      "openingHours": {
        "isOpen": false,
        "openFrom": "00:00:00",
        "openTo": "00:00:00"
      },
      "closed": true
    }
  ],
  "externalIds": null,
  "isWarehouse": true,
  "isPrimaryWarehouse": true,
  "isPublicVisible": false,
  "properties": null
}
```

In the Omnium GUI, the store would appear with a map location of any given coordinates.

![Image](https://docs.omnium.no/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fstores_sample.97494563.jpg&w=3840&q=75)

You can also edit store information in the GUI.

![Image](https://docs.omnium.no/_next/image?url=%2F_next%2Fstatic%2Fmedia%2Fstore_edit.fa936956.jpg&w=3840&q=75)

* * *

## Store Operations

### Create Store

Add a new store to Omnium by submitting a store object to the stores endpoint. If the store exists, it will be overridden by the object.

[Create store documentation](https://api.omnium.no/documentation/index.html#/Stores/put_api_Stores).

### Get Store

*   **Get all stores**: Retrieve a list of all stores, warehouses, and pickup points.

    *   [Get all stores documentation](https://api.omnium.no/documentation/index.html#/Stores/StoresGet)
*   **Get single store**: Retrieve information about a single store using its ID.

    *   [Get single store documentation](https://api.omnium.no/documentation/index.html#/Stores/StoresGet)

### Update Store

Update a store by submitting a store object.

[Update store documentation](https://api.omnium.no/documentation/index.html#/Stores/StoresPut).

### Delete Store

To delete a store, send a delete request to the stores endpoint.

Important

The store will be permanently deleted and cannot be recovered.

[Delete store documentation](https://api.omnium.no/documentation/index.html#/Stores/StoresDelete).

* * *

## More Store Information

### Store Opening Hours

A store has opening hours for every day of the week as separate properties (e.g., `mondayOpeningHours`, `tuesdayOpeningHours`, etc.). You can also create special opening hours for public holidays or other exceptions.

For more details, refer to the [Opening Hours Model documentation](https://api.omnium.no/documentation/index.html#/model-OmniumOpeningHours).

### Special Opening Hours Model

For special opening hours, refer to the [Special Opening Hours Model documentation](https://api.omnium.no/documentation/index.html#/model-OmniumSpecialOpeningHours).

* * *

## Store FAQ

### Creating a Store: What is a Store ID?

A store ID must be unique across all markets. Any duplicate IDs will overwrite existing stores. We recommend using URL-friendly IDs.
