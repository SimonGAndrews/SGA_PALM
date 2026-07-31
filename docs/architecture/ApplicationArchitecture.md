# Application Architecture

PALM needs one main application that works across the devices we already use.

The working choice is an Angular Progressive Web Application (PWA) for the main user interface.

Angular runs in the browser. It does not need a separate application server of its own. The built application can be served as static files and can call the PALM service over HTTPS.

The PWA gives PALM one installable application and one update path across supported desktop and mobile platforms. Angular remains independent of the hosting provider even when the first deployment uses Firebase Hosting.

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

The application presents assets, relationships, locations, and lifecycle history. The service owns the rules and the database updates.

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

Each guided workflow should send one meaningful request to the PALM service. The service validates it, updates current state, and records the lifecycle event.

## XState

XState may be useful for the guided workflows where several scans and decisions must happen in order.

Good candidates are workflows with:

- several clear steps
- retries or timeouts
- scanner input
- confirmation before submission
- recoverable errors

It should not be used for ordinary forms or simple screens where normal Angular state is enough.

Small device clients may use an equivalent lightweight finite-state machine for the same kind of guided workflow. They do not need to share the Angular implementation.

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

Where browser access to a USB Zebra printer is not practical, a PALM Local Agent can bridge between PALM print jobs and the printer. Whether Zebra Browser Print should also be supported remains an implementation decision.

## Offline operation

The first version should only report success after the PALM service confirms the transaction. Offline queues and synchronisation may be added later when a demonstrated use case justifies the ordering, duplication, and conflict-handling rules they require.