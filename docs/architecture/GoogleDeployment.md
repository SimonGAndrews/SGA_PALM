# Google Deployment

PALM is expected to use Google-hosted services for the shared application service and relational database.

The first deployment should stay simple and fit within free or low-cost service levels while PALM is being developed and used by one family.

The initial deployment baseline is Firebase Hosting for the Angular PWA, Firebase Authentication for family users, Cloud Run for the PALM service, and Cloud SQL for PostgreSQL. These remain working choices until pricing, limits, and the development workflow have been tested.

## Working shape

```text
Angular application
Espruino and other device clients
            |
            | HTTPS + JSON
            v
Google-hosted PALM service
            |
            v
Hosted relational database
```

The PALM service owns assets, events, relationships, validation, and transactions. Clients do not connect directly to the database.

## Current Google options

The initial building blocks are:

- Firebase Hosting for the Angular PWA
- Firebase Authentication for family users
- Cloud Run for the hosted HTTPS PALM service
- Cloud SQL for the managed PostgreSQL database
- PALM-issued identities and credentials for registered devices

Firebase Data Connect may be evaluated as a managed PostgreSQL access layer. It must not allow browser or device clients to bypass the PALM service for operations governed by PALM business rules. The final combination will be confirmed after a small working deployment has been tested.

## One service first

PALM should start as one deployable application service with one relational database.

Internally it can still have clear modules for assets, events, relationships, labels, devices and users. There is no need to split these into separate services at the start.

## Device clients

Adding ESP32 or Espruino clients does not require a separate Google infrastructure design.

They are additional clients of the same PALM service and use small purpose-built endpoints over HTTPS.

For example:

```http
POST /api/transactions/place-asset
```

```json
{
  "asset": "PLANT-0123",
  "container": "POT-0456"
}
```

The service validates the request, changes current state, and records the event in the same transaction.

Device endpoints should use short requests, clear responses and retry-safe operations.

## Local hardware

Some hardware cannot sensibly be controlled directly from a hosted service.

USB barcode scanners can normally be handled by the local application. USB Zebra printers will usually need a local print adapter or a computer connected to the printer.

```text
PALM service
    |
    v
Print job or ZPL
    |
    v
Local print adapter
    |
    v
Zebra printer
```

Where a Zebra printer has a suitable serial interface, an Espruino or ESP32 device may send the print data directly over UART.

PALM should own the label content and requested print operation. The local adapter owns the physical connection to the printer.

## Cost and operation

The first deployment should be designed for light family use:

- low request volumes
- a small database
- modest file and photo storage
- few concurrent users
- occasional device transactions

Cost limits, backups, database export and service monitoring will need to be checked during the first deployment trial.

The aim is not to design for large scale now. It is to keep a clean route to grow without making the first version difficult to run.

Cloud SQL is expected to be the main recurring infrastructure cost. Early deployment work should verify that the complete baseline remains suitable for long-term, low-volume family use rather than relying only on prototype or free-tier allowances.