---
layout: default
title: Data Model
---
To model the scenario where a practitioner has multiple relationships with organizations, and an organization has multiple relationships with practitioners, with each relationship potentially having one or multiple types (e.g., attending, consulting), a relational data model is suitable.

## Data Model
### Entities
1. Practitioner: Represents individual healthcare practitioners.
2. Organization: Represents healthcare organizations such as hospitals or clinics.
3. PractitionerOrganizationRelationship: Represents the relationship between a practitioner and an organization, including the types of the relationship.
4. RelationshipType: Represents the possible types of relationships (e.g., attending, consulting).

### Table Structures
**Practitioner**
- PractitionerID (Primary Key, Unique Identifier)
- FirstName (String, e.g., "John")
- LastName (String, e.g., "Doe")
- Specialty (String, e.g., "Cardiology")
- ContactInfo (String, optional)  

**Organization**
- OrganizationID (Primary Key, Unique Identifier)
- Name (String, e.g., "Springfield General Hospital")
- Address (String, optional)
- ContactInfo (String, optional)

**PractitionerOrganizationRelationship**
- RelationshipID (Primary Key, Unique Identifier)
- PractitionerID (Foreign Key referencing Practitioner.PractitionerID)
- OrganizationID (Foreign Key referencing Organization.OrganizationID)
- StartDate (Date, optional, when the relationship began)
- EndDate (Date, optional, when the relationship ended)
- Notes (String, optional)

**RelationshipType**
- RelationshipTypeID (Primary Key, Unique Identifier)
- Name (String, e.g., "Attending", "Consulting")
- Description (String, optional)

**PractitionerOrganizationRelationshipType (Link Table)**
- RelationshipID (Foreign Key referencing PractitionerOrganizationRelationship.RelationshipID)
- RelationshipTypeID (Foreign Key referencing RelationshipType.RelationshipTypeID)
- Primary Key (Composite Key: RelationshipID + RelationshipTypeID)


### Model Diagram
![Diagram Overview](/out/imageSource/modelDiagram/modeDiagram.png)

Detailed description of Feature 1. This page can include:

![Image description](/assets/images/becomePhysician.png)

- In-depth explanation
- Usage examples
- Configuration options
- Screenshots or diagrams
- Code samples

[← Back to Home]({{ '/' | relative_url }})