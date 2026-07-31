# Device Architecture

PALM will support small purpose-built devices as well as the main web application.

The first examples are Espruino devices used with barcode scanners in the garden, greenhouse or workshop.

A device might:

- scan a plant and a pot, then place the plant in the pot
- scan a box and a shelf, then record the move
- scan an asset and request a replacement label
- collect a few extra values before submitting a transaction
- send a print job to a nearby Zebra printer

## Keep devices focused

A device should do one small job well.

It may manage the local workflow:

```text
Choose operation
Scan first barcode
Scan second barcode
Enter any extra values
Submit
Show success or failure
```

It should not reproduce the PALM data model or business rules.

## Use transaction endpoints

Devices call stable endpoints that describe useful PALM actions.

For example:

```http
POST /api/transactions/place-asset
```

```json
{
  "assetId": "PLANT-0123",
  "containerId": "POT-0456"
}
```

The PALM service validates the request, changes the current state and records the event as one transaction.

Devices must not manipulate database records directly or coordinate several low-level updates themselves.

## Communication

Wi-Fi is the simplest first route for Espruino devices because they can call the PALM service directly using HTTP and JSON.

Bluetooth remains useful where Wi-Fi is unavailable or power use matters, but it will normally need a phone, tablet or local gateway between the device and PALM.

The device API should use:

- small JSON requests and responses
- clear result and error codes
- short-lived requests rather than permanent connections
- stable endpoint versions
- retry-safe transaction identifiers where needed

## Connection failure

For early versions, a device should normally confirm that PALM accepted the transaction before telling the user it succeeded.

Queued offline transactions can be added later, but they introduce ordering, duplication and conflict handling. They should be introduced only where a real use case needs them.

## Device identity

Each device should have its own PALM identity and credential.

This allows PALM to:

- limit what the device may do
- record which device submitted an event
- disable a lost or retired device
- distinguish user actions from automated actions

A barcode wand may be allowed to place and move assets but not delete them or change system settings.

The first implementation still needs to choose a credential mechanism. Device secrets, signed tokens, and client certificates are candidates. Embedded devices must not contain privileged database or general cloud credentials.

## Barcode content

A barcode should normally contain a stable PALM identifier, not changeable details such as plant name, location or year.

The device reads the identifier. PALM decides what the asset is and whether the requested transaction is valid.

## Label printing

PALM owns the label request and label content. A printer adapter handles the physical printer.

```text
PALM service
    |
    v
Print job
    |
    v
Local printer adapter or device
    |
    v
Zebra printer
```

This keeps the application independent of the connection used by a particular printer.

Possible adapters include:

- a Windows or Ubuntu application connected to the printer by USB
- a small local service on a Raspberry Pi or similar computer
- an Espruino or ESP32 device connected to a printer with a suitable UART interface
- a network-connected Zebra printer

For Zebra printers, the adapter can send ZPL generated from the PALM print job. The current preference is for PALM to select the template and generate the label content centrally, while the adapter owns transport and physical output. The exact ZPL boundary still needs testing against device constraints.

A USB-only printer cannot normally be driven from a UART without extra USB-host hardware, so the physical printer interface must be checked before choosing the adapter.

## Future automation

The same device model can later support greenhouse and workshop automation.

A controller may record events such as watering, measurement, maintenance or stock use. These are still ordinary PALM transactions submitted by an identified device.

The rule remains the same:

> Devices request meaningful PALM transactions. The PALM service owns the data and the rules.