---
title: SQL Server to SQL Server on Azure VM (Migration overview)
description: Learn about the different migration strategies when you want to migrate your SQL Server to SQL Server on Azure VMs. 
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.topic: how-to
author: markjones-msft
ms.author: markjon
ms.reviewer: chadam, mathoma
ms.date: 09/07/2021
---

# Migration overview: SQL Server to SQL Server on Azure VMs
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlvm.md)]

Learn about the different migration strategies to migrate your SQL Server to SQL Server on Azure Virtual Machines (VMs). 

You can migrate SQL Server running on-premises or on:

- SQL Server on Virtual Machines  
- Amazon Web Services (AWS) EC2 
- Amazon Relational Database Service (AWS RDS) 
- Compute Engine (Google Cloud Platform - GCP)

For other migration guides, see [Database Migration](/data-migration). 

## Overview

Migrate to [SQL Server on Azure Virtual Machines (VMs)](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) when you want to use the familiar SQL Server environment with OS control, and want to take advantage of cloud-provided features such as built-in VM high availability, [automated backups](../../virtual-machines/windows/automated-backup.md), and [automated patching](../../virtual-machines/windows/automated-patching.md). 

Save on costs by bringing your own license with the [Azure Hybrid Benefit licensing model](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md) or extend support for SQL Server 2008 and SQL Server 2008 R2 by getting [free security updates](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md). 


## Choose appropriate target

Azure Virtual Machines run in many different regions of Azure and also offer a variety of [machine sizes](../../../virtual-machines/sizes.md) and [Storage options](../../../virtual-machines/disks-types.md). 
When determining the correct size of VM and Storage for your SQL Server workload, refer to the [Performance Guidelines for SQL Server on Azure Virtual Machines.](../../virtual-machines/windows/performance-guidelines-best-practices-checklist.md#vm-size). To determine the VM size and storage requirements for your workload. it is recommended that these are sized through a Performance-Based [Azure Migrate Assessment](../../../migrate/concepts-assessment-calculation.md#types-of-assessments). If this is not an available option, see the following article on creating your own [baseline for performance](https://azure.microsoft.com/services/virtual-machines/sql-server/).

Consideration should also be made on the correct installation and configuration of SQL Server on a VM. It is recommended to use the [Azure SQL virtual machine image gallery](../../virtual-machines/windows/create-sql-vm-portal.md) as this allows you to create a SQL Server VM with the right version, edition, and operating system. This will also register the Azure VM with the SQL Server [Resource Provider](../../virtual-machines/windows/create-sql-vm-portal.md) automatically, enabling features such as Automated Backups and Automated Patching.

## Migration strategies

There are two migration strategies to migrate your user databases to an instance of SQL Server on Azure VMs: 
**migrate**, and **lift and shift**. 

The appropriate approach for your business typically depends on the following factors: 

- Size and scale of migration
- Speed of migration
- Application support for code change
- Need to change SQL Server Version, Operating System, or both.
- Supportability life cycle of your existing products
- Window for application downtime during migration

:::image type="content" source="media/sql-server-to-sql-on-azure-vm-individual-databases-guide/virtual-machine-migration-downtime.png" alt-text="virtual machine migration downtime":::

The following table describes differences in the two migration strategies: 
<br />

| **Migration strategy** | **Description** | **When to use** |
| --- | --- | --- |
| **Lift & shift** | Use the lift and shift migration strategy to move the entire physical or virtual SQL Server from its current location onto an instance of SQL Server on Azure VM without any changes to the operating system, or SQL Server version. To complete a lift and shift migration, see [Azure Migrate](../../../migrate/migrate-services-overview.md). <br /><br /> The source server remains online and services requests while the source and destination server synchronize data allowing for an almost seamless migration. | Use for single to very large-scale migrations, even applicable to scenarios such as data center exit. <br /><br /> Minimal to no code changes required to user SQL databases or applications, allowing for faster overall migrations. <br /><br />No additional steps required for migrating the Business Intelligence services such as  [SSIS](/sql/integration-services/sql-server-integration-services), [SSRS](/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports), and [SSAS](/analysis-services/analysis-services-overview). |
|**Migrate** | Use a migrate strategy when you want to upgrade the target SQL Server and/or operating system version. <br /> <br /> Select an Azure VM from Azure Marketplace or a prepared SQL Server image that matches the source SQL Server version. <br/> <br/> Use the [Azure SQL Migration extension for Azure Data Studio](../../../dms/migration-using-azure-data-studio.md) to migrate SQL Server database(s) to SQL Server on Azure virtual machines with minimal downtime. | Use when there is a requirement or desire to use features available in newer versions of SQL Server, or if there is a requirement to upgrade legacy SQL Server and/or OS versions that are no longer in support.  <br /> <br /> May require some application or user database changes to support the SQL Server upgrade. <br /><br />There may be additional considerations for migrating [Business Intelligence](#business-intelligence) services if in the scope of migration. |


## Lift and shift  

The following table details the available method for the **lift and shift** migration strategy to migrate your SQL Server database to SQL Server on Azure VMs: 
<br />

|**Method** | **Minimum source version** | **Minimum target version** | **Source backup size constraint** |  **Notes** |
| --- | --- | --- | --- | --- |
| [Azure Migrate](../../../migrate/index.yml) | SQL Server 2008 SP4| SQL Server 2008 SP4| [Azure VM storage limit](../../../index.yml) |  Existing SQL Server to be moved as-is to instance of SQL Server on an Azure VM. Can scale migration workloads of up to 35,000 VMs. <br /><br /> Source server(s) remain online and servicing requests during synchronization of server data, minimizing downtime. <br /><br /> **Automation & scripting**: [Azure Site Recovery Scripts](../../../migrate/how-to-migrate-at-scale.md) and [Example of scaled migration and planning for Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)|

> [!NOTE]
> It's now possible to lift and shift both your [failover cluster instance](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) and [availability group](sql-server-availability-group-to-sql-on-azure-vm.md) solution to SQL Server on Azure VMs using Azure Migrate. 

## Migrate  

Due to the ease of setup, the recommended migration approach is to take a native SQL Server [backup](/sql/t-sql/statements/backup-transact-sql) locally and then copy the file to Azure. This method supports larger databases (>1 TB) for all versions of SQL Server starting from 2008 and larger database backups (>1 TB). However, for databases starting in SQL Server 2014, that are smaller than 1 TB, and that have good connectivity to Azure, then [SQL Server backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) is the better approach. 

When migrating SQL Server databases to an instance of SQL Server on Azure VMs, it is important to choose an approach that suits when you need to cutover to the target server as this affects the application downtime window.

The following table details all available methods to migrate your SQL Server database to SQL Server on Azure VMs: 
<br />

|**Method** | **Minimum source version** | **Minimum target version** | **Source backup size constraint** | **Notes** |
| --- | --- | --- | --- | --- |
| **[Azure SQL Migration extension for Azure Data Studio](../../../dms/migration-using-azure-data-studio.md)** | SQL Server 2005 | SQL Server 2008 | [Azure VM storage limit](../../../index.yml) |  This is an easy to use wizard based extension in Azure Data Studio for migrating SQL Server database(s) to SQL Server on Azure virtual machines. Use compression to minimize backup size for transfer. <br /><br /> The Azure SQL Migration extension for Azure Data Studio provides both assessment and migration capabilities in a simple user interface.  |
| **[Backup to a file](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 | SQL Server 2008 SP4| [Azure VM storage limit](../../../index.yml) |  This is a simple and well-tested technique for moving databases across machines. Use compression to minimize backup size for transfer. <br /><br /> **Automation & scripting**: [Transact-SQL (T-SQL)](/sql/t-sql/statements/backup-transact-sql) and [AzCopy to Blob storage](../../../storage/common/storage-use-azcopy-v10.md)  |
| **[Backup to URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)** | SQL Server 2012 SP1 CU2 | SQL Server 2012 SP1 CU2| 12.8 TB for SQL Server 2016, otherwise 1 TB | An alternative way to move the backup file to the VM using Azure storage. Use compression to minimize backup size for transfer. <br /><br /> **Automation & scripting**:  [T-SQL or maintenance plan](/sql/relational-databases/backup-restore/sql-server-backup-to-url) |
| **[Database Migration Assistant (DMA)](/sql/dma/dma-overview)** | SQL Server 2005| SQL Server 2008 SP4| [Azure VM storage limit](../../../index.yml) |  The [DMA](/sql/dma/dma-overview) assesses SQL Server on-premises and then seamlessly upgrades to later versions of SQL Server or migrates to SQL Server on Azure VMs, Azure SQL Database or Azure SQL Managed Instance. <br /><br /> Should not be used on Filestream-enabled user databases.<br /><br /> DMA also includes capability to migrate [SQL and Windows logins](/sql/dma/dma-migrateserverlogins) and assess [SSIS Packages](/sql/dma/dma-assess-ssis). <br /><br /> **Automation & scripting**: [Command line interface](/sql/dma/dma-commandline) |
| **[Detach and attach](../../virtual-machines/windows/migrate-to-vm-from-sql-server.md#detach-and-attach-from-a-url)** | SQL Server 2008 SP4 | SQL Server 2014 | [Azure VM storage limit](../../../index.yml) | Use this method when you plan to [store these files using the Azure Blob storage service](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) and attach them to an instance of SQL Server on an Azure VM, particularly useful with very large databases or when the time to backup and restore is too long. <br /><br /> **Automation & scripting**:  [T-SQL](/sql/relational-databases/databases/detach-a-database#TsqlProcedure) and [AzCopy to Blob storage](../../../storage/common/storage-use-azcopy-v10.md)|
|**[Log shipping](sql-server-to-sql-on-azure-vm-individual-databases-guide.md#migrate)** | SQL Server 2008 SP4 (Windows Only) | SQL Server 2008 SP4 (Windows Only) | [Azure VM storage limit](../../../index.yml) | Log shipping replicates transactional log files from on-premises on to an instance of SQL Server on an Azure VM. <br /><br /> This provides minimal downtime during failover and has less configuration overhead than setting up an Always On availability group. <br /><br /> **Automation & scripting**: [T-SQL](/sql/database-engine/log-shipping/log-shipping-tables-and-stored-procedures)  |
| **[Distributed availability group](../../virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md#hybrid-it-disaster-recovery-solutions)** | SQL Server 2016| SQL Server 2016 | [Azure VM storage limit](../../../index.yml) |  A [distributed availability group](/sql/database-engine/availability-groups/windows/distributed-availability-groups) is a special type of availability group that spans two separate availability groups. The availability groups that participate in a distributed availability group do not need to be in the same location and include cross-domain support. <br /><br /> This method minimizes downtime, use when you have an availability group configured on-premises. <br /><br /> **Automation & scripting**: [T-SQL](/sql/t-sql/statements/alter-availability-group-transact-sql)  |
| | | | | |

&nbsp;
&nbsp;
&nbsp;&nbsp;
&nbsp;
&nbsp;
 
> [!TIP]
> - For large data transfers with limited to no network options, see [Large data transfers with limited connectivity](../../../storage/common/storage-solution-large-dataset-low-network.md).
> - It's now possible to lift and shift both your [failover cluster instance](sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) and [availability group](sql-server-availability-group-to-sql-on-azure-vm.md) solution to SQL Server on Azure VMs using Azure Migrate. 

### Considerations

The following is a list of key points to consider when reviewing migration methods:

- For optimum data transfer performance, migrate databases and files onto an instance of SQL Server on Azure VM using a compressed backup file. For larger databases, in addition to compression, [split the backup file into smaller files](/sql/relational-databases/backup-restore/back-up-files-and-filegroups-sql-server) for increased performance during backup and transfer. 
- If migrating from SQL Server 2014 or higher, consider [encrypting the backups](/sql/relational-databases/backup-restore/backup-encryption) to protect data during network transfer.
- To minimize downtime during database migration, use the Always On availability group option. 
- To minimize downtime without the overhead of configuring an availability group, use the log shipping option. 
- For limited to no network options, use offline migration methods such as backup and restore, or [disk transfer services](../../../storage/common/storage-solution-large-dataset-low-network.md) available in Azure.
- To also change the version of SQL Server on a SQL Server on Azure VM, see [change SQL Server edition](../../virtual-machines/windows/change-sql-server-edition.md).

## Business Intelligence 

There may be additional considerations when migrating SQL Server Business Intelligence services outside the scope of database migrations. 

### SQL Server Integration Services

You can migrate SQL Server Integration Services (SSIS) packages and projects in SSISDB to SQL Server on Azure VM using one of the two methods below. 

- Backup and restore the SSISDB from the source SQL Server instance to SQL Server on Azure VM. This will restore your packages in the SSISDB to the [Integration Services Catalog on your target SQL Server on Azure VM](/sql/integration-services/catalog/ssis-catalog).
- Re-deploy your SSIS packages on your target SQL Server on Azure VM using one of the [deployment options](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

If you have SSIS packages deployed as package deployment model, you can convert them before migration. See the [project conversion tutorial](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) to learn more. 


### SQL Server Reporting Services
To migrate your SQL Server Reporting Services (SSRS) reports to your target SQL Server on Azure VM, see [Migrate a Reporting Services Installation (Native Mode)](/sql/reporting-services/install-windows/migrate-a-reporting-services-installation-native-mode)

Alternatively, you can also migrate SSRS reports to paginated reports in Power BI. Use the [RDL Migration Tool](https://github.com/microsoft/RdlMigration) to help prepare and migrate your reports. Microsoft developed this tool to help customers migrate Report Definition Language (RDL) reports from their SSRS servers to Power BI. It's available on GitHub, and it documents an end-to-end walkthrough of the migration scenario. 

### SQL Server Analysis Services
SQL Server Analysis Services databases (multidimensional or tabular models) can be migrated from your source SQL Server to SQL Server on Azure VM using one of the following options:

-    Interactively using SSMS
-    Programmatically using Analysis Management Objects (AMO)
-    By script using XMLA (XML for Analysis)

See [Move an Analysis Services Database](/analysis-services/multidimensional-models/move-an-analysis-services-database?view=asallproducts-allversions&preserve-view=true) to learn more.

Alternatively, you can consider migrating your on-premises Analysis Services tabular models to [Azure Analysis Services](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/) or to [Power BI Premium by using the new XMLA read/write endpoints](/power-bi/admin/service-premium-connect-tools). 

## Server objects

Depending on the setup in your source SQL Server, there may be additional SQL Server features that will require manual intervention to migrate them to SQL Server on Azure VM by generating scripts in Transact-SQL (T-SQL) using SQL Server Management Studio and then running the scripts on the target SQL Server on Azure VM. Some of the commonly used features are:

- Logins and roles
- Linked server(s)
- External Data Sources
- Agent jobs
- Alerts
- Database Mail
- Replication

## Supported versions

As you prepare for migrating SQL Server databases to SQL Server on Azure VMs, be sure to consider the versions of SQL Server that are supported. For a list of current supported SQL Server versions on Azure VMs, please see [SQL Server on Azure VMs](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-server-vms).

## Migration assets 

For additional assistance, see the following resources that were developed for real world migration projects.

|Asset  |Description  |
|---------|---------|
|[Data workload assessment model and tool](https://www.microsoft.com/download/details.aspx?id=103130)| This tool provides suggested "best fit" target platforms, cloud readiness, and application/database remediation level for a given workload. It offers simple, one-click calculation and report generation that helps to accelerate large estate assessments by providing and automated and uniform target platform decision process.|
|[Perfmon data collection automation using Logman](https://www.microsoft.com/download/details.aspx?id=103114)|A tool that collects Perform data to understand baseline performance that assists in the migration target recommendation. This tool that uses logman.exe to create the command that will create, start, stop, and delete performance counters set on a remote SQL Server.|
|[Multiple-SQL-VM-VNet-ILB](https://www.microsoft.com/download/details.aspx?id=103104)|This whitepaper outlines the steps to setup multiple Azure virtual machines in a SQL Server Always On Availability Group configuration.|
|[Azure virtual machines supporting Ultra SSD per Region](https://www.microsoft.com/download/details.aspx?id=103105)|These PowerShell scripts provide a programmatic option to retrieve the list of regions that support Azure virtual machines supporting Ultra SSDs.|

The Data SQL Engineering team developed these resources. This team's core charter is to unblock and accelerate complex modernization for data platform migration projects to Microsoft's Azure data platform.

## Next steps

To start migrating your SQL Server databases to SQL Server on Azure VMs, see the [Individual database migration guide](sql-server-to-sql-on-azure-vm-individual-databases-guide.md). 

- For a matrix of the Microsoft and third-party services and tools that are available to assist you with various database and data migration scenarios as well as specialty tasks, see the article [Service and tools for data migration.](../../../dms/dms-tools-matrix.md)

- To learn more about Azure SQL see:
   - [Deployment options](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [SQL Server on Azure VMs](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure total Cost of Ownership Calculator](https://azure.microsoft.com/pricing/tco/calculator/) 


- To learn more about the framework and adoption cycle for Cloud migrations, see
   -  [Cloud Adoption Framework for Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Best practices for costing and sizing workloads migrate to Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- For information about licensing, see
   - [Bring your own license with the Azure Hybrid Benefit](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [Get free extended support for SQL Server 2008 and SQL Server 2008 R2](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)


- To assess the Application access layer, see [Data Access Migration Toolkit (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- For details on how to perform Data Access Layer A/B testing see [Database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
