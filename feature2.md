---
layout: default
title: Ensure data Accuracy
---


## Ensure Data Accuracy
To ensure data accuracy based on mutual attestation, we can implement logic that validates the agreement between a practitioner and an organization before inserting the relationship into the database. This can be achieved by creating a staging table for unverified relationships and adding a stored procedure that validates mutual attestation.

### Staging Table Schema
**Staging Table for Unverified Relationships**  
```
CREATE TABLE StagingPractitionerOrganizationRelationship (
    StagingID INT PRIMARY KEY AUTO_INCREMENT,
    PractitionerID INT NOT NULL,
    OrganizationID INT NOT NULL,
    RelationshipTypeID INT NOT NULL,
    Requester ENUM('Practitioner', 'Organization') NOT NULL,
    Status ENUM('Pending', 'Verified') DEFAULT 'Pending',
    CreatedAt TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (PractitionerID) REFERENCES Practitioner(PractitionerID) ON DELETE CASCADE,
    FOREIGN KEY (OrganizationID) REFERENCES Organization(OrganizationID) ON DELETE CASCADE,
    FOREIGN KEY (RelationshipTypeID) REFERENCES RelationshipType(RelationshipTypeID) ON DELETE CASCADE
);
```  


### Stored Procedure for Mutual Attestation Validation  
```
DELIMITER $$

CREATE PROCEDURE ValidateAndInsertRelationship()
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE pPractitionerID INT;
    DECLARE pOrganizationID INT;
    DECLARE pRelationshipTypeID INT;

    -- Cursor to process staging records
    DECLARE cur CURSOR FOR 
    SELECT PractitionerID, OrganizationID, RelationshipTypeID 
    FROM StagingPractitionerOrganizationRelationship 
    WHERE Status = 'Pending';

    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = 1;

    OPEN cur;

    read_loop: LOOP
        FETCH cur INTO pPractitionerID, pOrganizationID, pRelationshipTypeID;
        IF done THEN
            LEAVE read_loop;
        END IF;

        -- Check if mutual attestation exists
        IF EXISTS (
            SELECT 1
            FROM StagingPractitionerOrganizationRelationship 
            WHERE PractitionerID = pPractitionerID 
              AND OrganizationID = pOrganizationID 
              AND RelationshipTypeID = pRelationshipTypeID 
              AND Requester = 'Organization' 
              AND Status = 'Pending'
        )
        AND EXISTS (
            SELECT 1
            FROM StagingPractitionerOrganizationRelationship 
            WHERE PractitionerID = pPractitionerID 
              AND OrganizationID = pOrganizationID 
              AND RelationshipTypeID = pRelationshipTypeID 
              AND Requester = 'Practitioner' 
              AND Status = 'Pending'
        )
        THEN
            -- Insert verified relationship
            INSERT INTO PractitionerOrganizationRelationship (PractitionerID, OrganizationID, StartDate)
            VALUES (pPractitionerID, pOrganizationID, CURRENT_DATE());

            -- Update staging records as verified
            UPDATE StagingPractitionerOrganizationRelationship
            SET Status = 'Verified'
            WHERE PractitionerID = pPractitionerID 
              AND OrganizationID = pOrganizationID 
              AND RelationshipTypeID = pRelationshipTypeID;
        END IF;
    END LOOP;
    CLOSE cur;
END$$
DELIMITER ;
```



[← Back to Home]({{ '/' | relative_url }})