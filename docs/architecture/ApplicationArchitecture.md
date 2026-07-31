# Application Architecture

PALM needs one main application that works across the devices we already use.

The current working choice is Angular for the main user interface.

Angular runs in the browser. It does not need a separate application server of its own. The built application can be served as static files and can call the PALM service over HTTPS.

The same application should support:

- Windows and Ubuntu desktops
- tablets and phones
- ordinary keyboard and mouse use
- barcode scanners that act as keyboards
- camera scanning where it is useful

The application should be responsive rather than split into separate desktop and mobile products.

## Main parts

```text
Angular application
        |
        | HTTPS + JSON
        v
PALM service
        |
        v
Relational database
```

The application presents assets, relationships, locations and lifecycle history. The service owns the rules and the database updates.

The user interface should not contain business rules that also need to be repeated in device clients.

## Transactions

Some activities are better treated as guided transactions rather than ordinary forms.

Examples include:

- add a plant to a pot
- move a tray to a greenhouse bench
- place a component box on a shelf
- record a cutting from a parent plant
- print a replacement label

A typical workflow may be:

```text
Choose operation
Scan first asset
Scan second asset
Enter any extra values
Confirm
Submit
```

The application sends one meaningful request to the PALM service. The service validates it, updates current state and records the lifecycle event.

## XState

XState may be useful for the guided workflows where several scans and decisions must happen in order.

Good candidates are workflows with:

- several clear steps
- retries or timeouts
- scanner input
- confirmation before submission
- recoverable errors

It should not be used for ordinary forms or simple screens where normal Angular state is enough.

## Barcode input

PALM should accept barcode input from several sources without changing the asset model:

- USB scanners acting as keyboards
- serial scanners connected through a local client
- phone or tablet cameras
- purpose-built Espruino devices

The barcode normally carries a stable PALM identifier. The service decides what the asset is and which operations are valid.

## Desktop packaging

A packaged desktop application may be useful later, especially where direct access to local hardware is needed. It is not required for the first version.

The browser application and PALM service remain the main architecture.