# nShift Delivery

Source: https://docs.omnium.no/docs/plugins/Shipments/nshiftDelivery

> Integration with nShift Delivery (Unifaun) for shipment booking in Omnium.

[nShift Delivery API](https://helpcenter.nshift.com/hc/en-us/categories/4407954390802-Delivery)

Omnium integrates with nShift Delivery (formerly Unifaun) for comprehensive shipping management, including delivery checkout widgets, shipment booking, label generation, and direct printing.

* * *

## Configuration Steps

1.  Navigate to: **Configuration > Settings > Orders > Shipping providers > Add New**

* * *

## Required Settings

| Field | Description | Example |
| --- | --- | --- |
| **Name** | Internal provider name | `nShift` |
| **Display Name** | Name shown to users | `nShift Delivery` |
| **Shipping Provider** | Select provider type | `Unifaun` |
| **Merchant ID** | Your nShift user/merchant ID | `user@company.com` |
| **Merchant Secret** | Bearer token for API authentication | `your-api-token` |
| **Base URL** | nShift API endpoint | `https://api.unifaun.com` |
| **Vue Template** | UI template for widget display | `unifaun-provider` |

### Optional Settings

| Field | Description |
| --- | --- |
| **Add Tax On Provider Price** | Add tax to prices returned by nShift |
| **Add Product Names To Label** | Include product names/SKUs on shipping labels |
| **Print Label** | Enable label printing (default: true) |
| **Tracking URL** | Template URL with `{languageCode}` and `{orderNumber}` placeholders |

### Widget Configuration

To enable the nShift delivery checkout widget in Omnium:

1.  Set **Vue Template** to `unifaun-provider`
2.  Add a property with key `UseWidget` and value `true`

* * *

## Shipment Options

### Adding shipment options for each market

Under each market, define the shipping options available in your webshop. You have two booking methods:

### Option 1: Book based on PrepareId (Recommended for Widget Users)

If your webshop uses the nShift widget at checkout, retrieve a `prepareId` from the user session and store it on the Omnium cart or order in the property list:

```json
{
  "properties": [
    {
      "key": "UnifaunPrepareId",
      "value": "6f977858-35b8-4d5f-8fa6-5d46456543"
    }
  ]
}
```

When using prepareId, configure a single shipping option with:

*   **Shipping Method**: `Unifaun_deliveryCheckout`

### Option 2: Direct Booking

Configure individual shipping options for each delivery method:

| Field | Description |
| --- | --- |
| **Shipping Provider** | Set to `Unifaun` |
| **Template** | `Standard` for home delivery, `PickUp Point` for pickup points |
| **Delivery Type** | `Delivery` (sent to customer) or `Pickup` (collected at store) |
| **Display Name** | User-friendly name |
| **Shipping Method** | Internal name for mapping order shipments |
| **Shipment Product** | Service ID from nShift |
| **Shipment Return Product** | Service ID for return bookings |

### Show the order number on the shipping label

On a direct booking, Omnium sends the order number to nShift as `orderNo` and `receiverReference`. The sender reference (`senderReference`) is the contents of the first parcel: the shipment's goods description, or the product names when **Add Product Names To Label** is enabled. On a prepareId booking, the references and parcel contents come from the prepared shipment. Some carrier label layouts, such as DHL's, print the sender reference as the visible reference on the label.

To send the order number as the sender reference instead, add this property:

| Property Key | Value | Description |
| --- | --- | --- |
| `UseOrderNumberAsSenderReference` | `true` | Set it on a shipment option to apply it to that option only, or on the shipping provider to apply it to all its shipment options |

The setting is on when either the shipment option or the shipping provider has it set to `true`. Setting it to `false` on a shipment option does not turn it off when the provider has it set to `true`.

The setting applies to direct bookings, prepareId bookings, and return bookings, and it changes only the sender reference. If the order has no order number, the sender reference is left as it would be without the setting.

* * *

## Store Configuration

### QuickId Configuration

nShift supports QuickId for simplified sender identification. Configure on the store or shipment option:

| Property | Description |
| --- | --- |
| **QuickId** | nShift sender quick ID |
| **PreferQuickId** | Set to `true` to use only QuickId without full address details |

### Printer Configuration

For direct printing, configure the printer ID:

| Location | Property Key |
| --- | --- |
| User preferences | `UnifaunPrinterId` |
| Store properties | `UnifaunPrinterId` |

* * *

## Delivery Checkout / Delivery Options

### Custom Fields

Configure custom fields for delivery checkout queries using the **DeliveryCheckoutCustomFields** property. Use pipe-delimited `key:value` pairs:

| Field | Description |
| --- | --- |
| `MaxVolume` | Maximum package volume (m³) |
| `TotalWeight` | Total shipment weight (kg) |
| `MaxLength` | Maximum length (cm) |
| `MaxWidth` | Maximum width (cm) |
| `MaxHeight` | Maximum height (cm) |
| `DangerousGoods` | Boolean flag for dangerous goods |
| `IsB2B` | Boolean flag for B2B orders |
| `TotalPrice` | Order total price |
| `PickUpInStoreNotPossible` | Boolean for pickup availability |
| `AcquisitionGroup` | Product acquisition group parameters |
| `FreightClass` | Minimum freight class from products |

### Additional Query Parameters

Add custom query parameters to delivery checkout requests using the order property:

*   **Key**: `UnifaunDeliveryOptions_AdditionalQueryStringParameters`

* * *

## Return Shipments

### Configuration

1.  Set **Shipment Return Product** on the shipment option with the nShift service ID for returns
2.  The system automatically reverses sender/recipient addresses for return bookings

### Special Handling for Bring Carriers

For Bring carriers (products starting with 'b'), return bookings use direct consignment requests instead of the prepareId method.

* * *

## Label Management

### PDF Configuration

Labels are generated with the following default settings:

*   **Target 1**: `thermo-se` (Swedish thermal printer)
*   **Target 2**: `laser-a4` (A4 laser printer)

### Label Storage

Labels are uploaded to Azure Blob Storage in the `unifaunshippinglabels` container.

### Direct Printing

To enable direct printing:

1.  Configure **UnifaunPrinterId** on user preferences or store
2.  Labels are sent to the printer queue automatically
3.  Batch processing supports up to 50 shipments per batch

* * *

## Dangerous Goods Support

Products can be marked with dangerous goods flags. If ANY flag is true, the order is marked as containing dangerous goods:

*   Explosive
*   Corrosive
*   Oxidizing
*   Toxic
*   Harmful
*   Strong odor
*   Environmentally hazardous
*   Gas under pressure
*   Aerosol
*   Harmful to health
*   Flammable

Store dangerous goods information as JSON in the product's `DangerousGoods` property.

* * *

## API Endpoints

| Endpoint | Purpose |
| --- | --- |
| `GET /rs-extapi/v1/delivery-checkouts/{merchantId}` | Query delivery options |
| `POST /rs-extapi/v1/shipments/` | Book direct shipments |
| `POST /rs-extapi/v1/delivery-checkouts/{merchantId}` | Prepare shipments (creates UnifaunPrepareId) |
| `GET /rs-extapi/v1/prepared-shipments/{orderId}` | Get prepared shipment data |
| `POST /rs-extapi/v1/prepared-shipments/{prepareId}/shipments` | Book prepared shipment |
| `GET /rs-extapi/v1/shipments/{shipmentId}/pdfs/{pdfId}` | Download label PDF |
| `POST /rs-extapi/v1/print/shipments/{shipmentId}/prints/{labelId}/printjobs` | Submit to printer |

* * *

## Error Handling

### Error Keys

| Error Key | Description |
| --- | --- |
| `UnifaunBookingError` | Booking failure for standard shipments |
| `UnifaunBookingReturnError` | Booking failure for return shipments |

Errors are stored on the Order entity and automatically removed on successful booking.

* * *

## Supported Features

| Feature | Supported |
| --- | --- |
| Delivery checkout widget | Yes |
| Outbound shipment booking | Yes |
| Return shipment booking | Yes |
| PrepareId-based booking | Yes |
| Direct shipment booking | Yes |
| Label generation (PDF) | Yes |
| Direct printing | Yes |
| Pickup point selection | Yes (via widget) |
| Dangerous goods handling | Yes |
| B2B/B2C differentiation | Yes |
| Tax calculations | Yes |
| Multi-parcel shipments | Yes |

* * *

## Troubleshooting

### Common Issues

1.  **Booking fails with invalid phone number**: The system validates phone numbers against market patterns. Ensure the phone number matches the destination country format.

2.  **PrepareId not found**: Verify the `UnifaunPrepareId` property is set on the order or shipment properties.

3.  **Widget not loading**: Check that `UseWidget` property is set to `true` and Vue Template is `unifaun-provider`.
