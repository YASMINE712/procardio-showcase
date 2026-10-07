# One request, end to end

An employer can understand much of ProCardio's architecture by following a single edit. This walkthrough uses a fictional organization and a generic workflow record; it exposes no application fields or medical content.

![A request moves through identity, permissions, scoped data access and response](../assets/request-flow.svg)

## Example: editing an existing record

```mermaid
sequenceDiagram
    actor Person as Hospital A user
    participant UI as React interface
    participant API as Django API
    participant Auth as Authentication and permissions
    participant DB as PostgreSQL
    Person->>UI: Edit a workflow record
    UI->>API: Submit the change with authentication
    API->>Auth: Validate token and resolve the account
    Auth-->>API: Authenticated user and hospital context
    API->>API: Check permission for this operation
    API->>DB: Find record within Hospital A scope
    alt Record exists within that scope
        DB-->>API: Scoped record
        API->>API: Validate accepted values
        API->>DB: Persist the accepted change
        API-->>UI: Return the saved representation
        UI-->>Person: Refresh the visible state
    else Record is absent from that scope
        DB-->>API: No matching record
        API-->>UI: Return an access or lookup error
    end
```

The sequence simplifies endpoint-specific details. It describes the application's separation of responsibilities rather than a literal API contract.

## Why each boundary matters

| Boundary | Engineering question | Value |
| --- | --- | --- |
| Authentication | Who is making the request, and is the account active? | Establishes an accountable application identity |
| Authorization | May that role perform this operation? | Separates access to information from permission to change it |
| Tenant scoping | Does the requested object belong to the authenticated hospital context? | Keeps ordinary hospital workflows within their organization |
| Validation | Are the submitted values accepted by this operation? | Gives persistence a defined input contract |
| Persistence and response | What was actually saved? | Lets the interface reconcile edits with server state |

For nested objects, the ownership check follows the parent record. Checking the parent is essential when a child object's identifier alone does not express its hospital context.

## Related design decisions

**Frontend state and server state have different jobs.** Local state supports editing and immediate interaction; the query cache represents fetched data. Mutation handling connects the two after a save.

**Configuration is part of the workflow.** Shared definitions and local overrides determine which controls are presented and how they are organized. Server permission checks remain a separate responsibility.

**Structured information can be reused.** Saved records can feed the report context, graphical views, and assistance features. Each consumer has its own trigger and review step; persistence does not imply that every export is regenerated immediately.

## A useful demonstration

Show a fictional Hospital A account opening one of its records, a role with restricted editing, and an attempted lookup outside A's scope. Explain the expected behavior before showing the result. This is a proposed demonstration scenario, not a claim that the entire authorization surface has been tested by the public repository.
