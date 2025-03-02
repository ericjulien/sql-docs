---
title: "Authentication in SQL Server"
description: "Learn about logins and authentication in SQL Server when using ADO.NET and where to find additional resources."
ms.date: "09/26/2019"
dev_langs: 
  - "csharp"
ms.assetid: 646ddbf5-dd4e-4285-8e4a-f565f666c5cc
ms.prod: sql
ms.prod_service: connectivity
ms.technology: connectivity
ms.topic: conceptual
author: David-Engel
ms.author: v-davidengel
ms.reviewer: v-kaywon
---
# Authentication in SQL Server

[!INCLUDE[Driver_ADONET_Download](../../../includes/driver_adonet_download.md)]

SQL Server supports two authentication modes, Windows authentication mode and mixed mode.  
  
- Windows authentication is the default, and is often referred to as integrated security because this SQL Server security model is tightly integrated with Windows. Specific Windows user and group accounts are trusted to log in to SQL Server. Windows users who have already been authenticated do not have to present additional credentials.  
  
- Mixed mode supports authentication both by Windows and by SQL Server. User name and password pairs are maintained within SQL Server.  
  
> [!IMPORTANT]
> We recommend using Windows authentication wherever possible. Windows authentication uses a series of encrypted messages to authenticate users in SQL Server. When SQL Server logins are used, SQL Server login names and encrypted passwords are passed across the network, which makes them less secure.  
  
With Windows authentication, users are already logged onto Windows and do not have to log on separately to SQL Server. The following `SqlConnection.ConnectionString` specifies Windows authentication without requiring users to provide a user name or password.  
  
```csharp
"Server=MSSQL1;Database=AdventureWorks;Integrated Security=true;"
```  
  
> [!NOTE]
> Logins are distinct from database users. You must map logins or Windows groups to database users or roles in a separate operation. You then grant permissions to users or roles to access database objects.  
  
## Authentication scenarios  
Windows authentication is usually the best choice in the following situations:  
  
- There is a domain controller.  
  
- The application and the database are on the same computer.  
  
- You are using an instance of SQL Server Express or LocalDB.  
  
SQL Server logins are often used in the following situations:  
  
- If you have a workgroup.  
  
- Users connect from different, non-trusted domains.  
  
- Internet applications, such as ASP.NET.  
  
> [!NOTE]
> Specifying Windows authentication does not disable SQL Server logins. Use the ALTER LOGIN DISABLE Transact-SQL statement to disable highly-privileged SQL Server logins.  
  
## Login types  
SQL Server supports three types of logins:  
  
- A local Windows user account or trusted domain account. SQL Server relies on Windows to authenticate the Windows user accounts.  
  
- Windows group. Granting access to a Windows group grants access to all Windows user logins that are members of the group.  
  
- SQL Server login. SQL Server stores both the username and a hash of the password in the master database, by using internal authentication methods to verify login attempts.  
  
> [!NOTE]
> SQL Server provides logins created from certificates or asymmetric keys that are used only for code signing. They cannot be used to connect to SQL Server.  
  
## Mixed mode authentication  
If you must use mixed mode authentication, you must create SQL Server logins, which are stored in SQL Server. You then have to supply the SQL Server user name and password at run time.  
  
> [!IMPORTANT]
> SQL Server installs with a SQL Server login named `sa` (an abbreviation of "system administrator"). Assign a strong password to the `sa` login and do not use the `sa` login in your application. The `sa` login maps to the `sysadmin` fixed server role, which has irrevocable administrative credentials on the whole server. There are no limits to the potential damage if an attacker gains access as a system administrator. All members of the Windows `BUILTIN\Administrators` group (the local administrator's group) are members of the `sysadmin` role by default, but can be removed from that role.  
  
> [!IMPORTANT]
> Concatenating connection strings from user input can leave you vulnerable to a connection string injection attack. Use the <xref:Microsoft.Data.SqlClient.SqlConnectionStringBuilder> to create syntactically valid connection strings at run time. 
  
## External resources  
For more information, see the following resources.  
  
|Resource|Description|  
|--------------|-----------------|  
|[Principals](../../../relational-databases/security/authentication-access/principals-database-engine.md)|Describes logins and other security principals in SQL Server.|  
  
## Next steps
- [Application security scenarios in SQL Server](application-security-scenarios-sql-server.md)
