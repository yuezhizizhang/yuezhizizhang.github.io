---
layout: post
title:  "Bulk Insert And Update using Stored Procedure"
categories: "stored-procedure sql-server"
---

In this article, I will explain how to do bulk insert and update records with stored procedure by INNER JOIN and LEFT JOIN tables.

**To start with**, we have a simple table which holds user names and ages.

```sql
CREATE TABLE [dbo].[SimpleUser]
(
	[SimpleUserID]      INT              IDENTITY(1, 1) NOT NULL, 
    [Name]              NVARCHAR(256)    NOT NULL,
    [Age]               INT              NOT NULL
    CONSTRAINT [PK_SimpleUser] PRIMARY KEY CLUSTERED ([SimpleUserID] ASC)
)
```

**The stored procedure** will perform bulk inserts and updates of user records. The passed in parameter is a list of users. If the user name already exists (Out of simple sake, we assume user names must be unique), then we update the age of the corresponding user. Otherwise, it is be a new user so that we will insert it into the table.

### Step 1: User-defined User List Data Type

The parameter of the stored procedure is a user list. We can define the user list as a table type. We make Name column primary key so that there is no duplicate name in the user list.

```sql
CREATE TYPE dbo.User_List_DataType AS TABLE(
	[Name]  NVARCHAR(256)    PRIMARY KEY NOT NULL,
	[Age]   INT              NOT NULL
)
```

### Step 2: Bulk inserts and updates by INNER JOIN and LEFT JOIN tables

We INNER JOIN the [dbo][SimpleUser] table with the passed in user list. If the name exists in the [dbo][SimpleUser] table, we update the age of the record. Otherwise, we insert the new user into the table.

```sql
CREATE PROCEDURE [dbo].[SimpleUser_Save]
    @Users       [dbo].[User_List_DataType]    READONLY
AS
BEGIN
    SET NOCOUNT ON

    BEGIN TRY
        BEGIN TRAN

            --Update by INNER JOIN

            UPDATE su
            SET su.Age = tsu.Age
            FROM [dbo].[SimpleUser] su
                INNER JOIN @Users   tsu
                ON su.Name = tsu.Name

            --Insert by LEFT JOIN

            INSERT INTO [dbo].[SimpleUser] (
                Name,
                Age
            )
            SELECT
                tsu.Name,
                tsu.Age
            FROM @Users tsu
                LEFT JOIN [dbo].[SimpleUser] su
                ON su.Name = tsu.Name
            WHERE su.Name IS NULL

        COMMIT TRAN
    END TRY
    BEGIN CATCH 
        DECLARE
            @errorNumber    int, 
            @errorSeverity  int,
            @errorState     int,
            @errorMessage   nvarchar(4000), 
            @errorProc      nvarchar(4000), 
            @errorLine      int;
        SELECT
            @errorNumber = ERROR_NUMBER(), 
            @errorSeverity = ERROR_SEVERITY(), 
            @errorState = ERROR_STATE(), 
            @errorMessage = ERROR_MESSAGE(), 
            @errorProc = ERROR_PROCEDURE(),
            @errorLine = ERROR_LINE()
       
        RAISERROR (N'Error: %d, Severity: %d, State: %d, Message: "%s", Procedure: "%s",  Line: %d', 10, 1, @errorNumber, @errorSeverity, @errorState, @errorMessage, @errorProc, @errorLine)
    END CATCH 
END
```

### Step 3: Exec stored procedure

```sql
DECLARE
    @Users [dbo].[User_List_DataType]

INSERT INTO @Users VALUES
    ('Jim',9),
    ('Luca',10)

EXEC [dbo].[SimpleUser_Save]
    @Users = @Users
```