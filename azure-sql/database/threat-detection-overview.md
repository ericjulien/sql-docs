---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: Advanced Threat Protection detects anomalous database activities indicating potential security threats in Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse Analytics.
services:
  - "sql-database"
ms.service: sql-db-mi
ms.subservice: security
ms.custom:
  - "sqldbrb=2"
ms.topic: conceptual
author: davidtrigano
ms.author: datrigan
ms.reviewer: wiassaf, vanto, sstein, mathoma
ms.date: 06/09/2021
tags: azure-synapse
monikerRange: "= azuresql || = azuresql-db || = azuresql-mi"
---

# SQL Advanced Threat Protection
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)] :::image type="icon" source="../media/applies-to/yes.png" border="false":::SQL Server on Azure VM :::image type="icon" source="../media/applies-to/yes.png" border="false":::Azure Arc-enabled SQL Server

Advanced Threat Protection for [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md), [Azure Synapse Analytics](/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is), [SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) and [Azure Arc-enabled SQL Server](/sql/sql-server/azure-arc/overview) detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases.

Advanced Threat Protection is part of the [Microsoft Defender for SQL](/azure/security-center/defender-for-sql-introduction) offering, which is a unified package for advanced SQL security capabilities. Advanced Threat Protection can be accessed and managed via the central Microsoft Defender for SQL portal.

## Overview

Advanced Threat Protection provides a new layer of security, which enables customers to detect and respond to potential threats as they occur by providing security alerts on anomalous activities. Users receive an alert upon suspicious database activities, potential vulnerabilities, and SQL injection attacks, as well as anomalous database access and queries patterns. Advanced Threat Protection integrates alerts with [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/), which include details of suspicious activity and recommend action on how to investigate and mitigate the threat. Advanced Threat Protection makes it simple to address potential threats to the database without the need to be a security expert or manage advanced security monitoring systems.

For a full investigation experience, it is recommended to enable auditing, which writes database events to an audit log in your Azure storage account.  To enable auditing, see [Auditing for Azure SQL Database and Azure Synapse](./auditing-overview.md) or [Auditing for Azure SQL Managed Instance](../managed-instance/auditing-configure.md).

## Alerts

Advanced Threat Protection detects anomalous activities indicating unusual and potentially harmful attempts to access or exploit databases. For a list of alerts, see the [Alerts for SQL Database and Azure Synapse Analytics in Microsoft Defender for Cloud](/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse).

## Explore detection of a suspicious event

You receive an email notification upon detection of anomalous database activities. The email provides information on the suspicious security event including the nature of the anomalous activities, database name, server name, application name, and the event time. In addition, the email provides information on possible causes and recommended actions to investigate and mitigate the potential threat to the database.

![Anomalous activity report](./media/threat-detection-overview/anomalous_activity_report.png)

1. Click the **View recent SQL alerts** link in the email to launch the Azure portal and show the Microsoft Defender for Cloud alerts page, which provides an overview of active threats detected on the database.

   ![Activity threats](./media/threat-detection-overview/active_threats.png)

1. Click a specific alert to get additional details and actions for investigating this threat and remediating future threats.

   For example, SQL injection is one of the most common Web application security issues on the Internet that is used to attack data-driven applications. Attackers take advantage of application vulnerabilities to inject malicious SQL statements into application entry fields, breaching or modifying data in the database. For SQL Injection alerts, the alert’s details include the vulnerable SQL statement that was exploited.

   ![Specific alert](./media/threat-detection-overview/specific_alert.png)

## Explore alerts in the Azure portal

Advanced Threat Protection integrates its alerts with [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/). Live SQL Advanced Threat Protection tiles within the database and SQL Microsoft Defender for Cloud blades in the Azure portal track the status of active threats.

Click **Advanced Threat Protection alert** to launch the Microsoft Defender for Cloud alerts page and get an overview of active SQL threats detected on the database.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="advanced threat protection alerts in database overview":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="advanced threat protection in Defender for SQL":::

## Next steps

- Learn more about [Advanced Threat Protection in Azure SQL Database & Azure Synapse](threat-detection-configure.md).
- Learn more about [Advanced Threat Protection in Azure SQL Managed Instance](../managed-instance/threat-detection-configure.md).
- Learn more about [Microsoft Defender for SQL](azure-defender-for-sql.md).
- Learn more about [Azure SQL Database auditing](auditing-overview.md)
- Learn more about [Microsoft Defender for Cloud](/azure/security-center/security-center-introduction)
 For more information on pricing, see the [Azure SQL Database pricing page](https://azure.microsoft.com/pricing/details/sql-database/)