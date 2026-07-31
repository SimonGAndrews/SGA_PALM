# Architecture Overview

PALM is a personal asset and lifecycle management system.

At its heart are two ideas:

- everything we manage is an asset
- things happen to assets throughout their lives; those are events

A plant, pot, tray, shelf, storage box, tool or component can all be treated as assets. Their type, properties and relationships differ, but the underlying model is the same.

## What PALM needs to do

PALM should answer practical questions such as:

- what is this?
- where is it now?
- what is it related to?
- what has happened to it?
- who or what recorded the change?

The system must work for ordinary family use while also supporting small purpose-built devices in the garden, greenhouse and workshop.

## Main parts

```text
Browser, phone or tablet
Espruino and other device clients
            │
            │ HTTP and JSON
            ▼
        PALM service
            │
            ├── assets and relationships
            ├── lifecycle transactions
            ├── event history
            ├── labels and print jobs
            └── users and devices
            │
            ▼
     Relational database
```

The first implementation should be one application service with one relational database. Clear internal modules are useful; separate microservices are not needed.

## Current state and history

PALM keeps normal relational data for current state and an event history for lifecycle.

For example, moving a plant to a pot should be one transaction that:

1. checks that the plant and pot exist
2. checks that the move is valid
3. updates the current relationship
4. records the event
5. returns a clear result to the client

This gives straightforward SQL queries for current state without losing the history of how that state was reached.

PALM is not intended to be a fully event-sourced system. Events are first-class records, but routine use should not require rebuilding current state from the full event stream.

## Clients

The main user interface should be a responsive web application that works on Windows, Ubuntu, phones and tablets.

Dedicated devices can support focused jobs where a scanner, buttons, small display or fixed location make them more convenient than the main application.

All clients use the same PALM service. They do not connect directly to the database.

## Transactions, not record editing

Clients ask PALM to carry out meaningful pieces of work.

Examples:

- place a plant in a pot
- move a box to a shelf
- record a cutting from a parent plant
- consume a component
- print a label

The service owns the rules and records the matching events.

## Deployment

PALM should be able to run as a small hosted service so that family users and Wi-Fi devices can reach it from different locations.

The design should also keep local hardware practical. Label printers, scanners and Bluetooth devices may need a local client or adapter even when the main PALM service is hosted.

The deployment choice should not change the core asset, event or transaction model.