---
title: Configure storage for SQL Server VMs | Microsoft Docs
description: This topic describes how Azure configures storage for SQL Server VMs during provisioning (Azure Resource Manager deployment model). It also explains how you can configure storage for your existing SQL Server VMs.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
tags: azure-resource-manager

ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management

ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2021
ms.author: pamela
ms.reviewer: mathoma
---
# Configure storage for SQL Server VMs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

This article teaches you how to configure your storage for your SQL Server on Azure Virtual Machines (VMs).

SQL Server VMs deployed through marketplace images automatically follow default [storage best practices](performance-guidelines-best-practices-storage.md) which can be modified during deployment. Some of these configuration settings can be changed after deployment.


## Prerequisites

To use the automated storage configuration settings, your virtual machine requires the following characteristics:

* Provisioned with a [SQL Server gallery image](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) or registered with the [SQL IaaS extension]().
* Uses the [Resource Manager deployment model](/azure/azure-resource-manager/management/deployment-models).
* Uses [premium SSDs](/azure/virtual-machines/disks-types).

## New VMs

The following sections describe how to configure storage for new SQL Server virtual machines.

### Azure portal

When provisioning an Azure VM using a SQL Server gallery image, select **Change configuration** under **Storage** on the **SQL Server Settings** tab to open the **Configure storage** page. You can either leave the values at default, or modify the type of disk configuration that best suits your needs based on your workload.

![Screenshot that highlights the SQL Server settings tab and the Change configuration option.](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

Choose the drive location for your data files and log files, specifying the disk type, and number of disks. Use the IOPS values to determine the best storage configuration to meet your business needs. Choosing premium storage  sets the caching to *ReadOnly* for the data drive, and *None* for the log drive as per [SQL Server VM performance best practices](./performance-guidelines-best-practices-checklist.md).

![SQL Server VM Storage Configuration During Provisioning](./media/storage-configuration/sql-vm-storage-configuration.png)

The disk configuration is completely customizable so that you can configure the storage topology, disk type and IOPs you need for your SQL Server VM workload. You also have the ability to use UltraSSD (preview) as an option for the **Disk type** if your SQL Server VM is in one of the supported regions (East US 2, SouthEast Asia and North Europe) and you've enabled [ultra disks for your subscription](/azure/virtual-machines/disks-enable-ultra-ssd).

Configure your tempdb database settings under **Tempdb storage**, such as the location of the database files, as well as the number of files, initial size, and autogrowth size in MB. Currently, during deployment, the max number of tempdb files is 8, but more files can be added after the SQL Server VM is deployed.

![Screenshot that shows where you can configure the tempdb storage for your SQL VM](./media/create-sql-vm-portal/storage-configuration-tempdb-storage.png)

Additionally, you have the ability to set the caching for the disks. Azure VMs have a multi-tier caching technology called [Blob Cache](/azure/virtual-machines/premium-storage-performance#disk-caching) when used with [Premium Disks](/azure/virtual-machines/disks-types#premium-ssds). Blob Cache uses a combination of the Virtual Machine RAM and local SSD for caching.

Disk caching for Premium SSD can be *ReadOnly*, *ReadWrite* or *None*.

- *ReadOnly* caching is highly beneficial for SQL Server data files that are stored on Premium Storage. *ReadOnly* caching brings low read latency, high read IOPS, and throughput as, reads are performed from cache, which is within the VM memory and local SSD. These reads are much faster than reads from data disk, which is from Azure Blob storage. Premium storage does not count the reads served from cache towards the disk IOPS and throughput. Therefore, your applicable is able to achieve higher total IOPS and throughput.
- *None* cache configuration should be used for the disks hosting SQL Server Log file as the log file is written sequentially and does not benefit from *ReadOnly* caching.
- *ReadWrite* caching should not be used to host SQL Server files as SQL Server does not support data consistency with the *ReadWrite* cache. Writes waste capacity of the *ReadOnly* blob cache and latencies slightly increase if writes go through *ReadOnly* blob cache layers.


   > [!TIP]
   > Be sure that your storage configuration matches the limitations imposed by the the selected VM size. Choosing storage parameters that exceed the performance cap of the VM size will result in warning: `The desired performance might not be reached due to the maximum virtual machine disk performance cap`. Either decrease the IOPs by changing the disk type, or increase the performance cap limitation by increasing the VM size. This will not stop provisioning.


Based on your choices, Azure performs the following storage configuration tasks after creating the VM:

* Creates and attaches Premium SSDs to the virtual machine.
* Configures the data disks to be accessible to SQL Server.
* Configures the data disks into a storage pool based on the specified size and performance (IOPS and throughput) requirements.
* Associates the storage pool with a new drive on the virtual machine.
* Optimizes this new drive based on your specified workload type (Data warehousing, Transactional processing, or General).

For a full walkthrough of how to create a SQL Server VM in the Azure portal, see [the provisioning tutorial](./create-sql-vm-portal.md).



### Resource Manager templates

If you use the following Resource Manager templates, two premium data disks are attached by default, with no storage pool configuration. However, you can customize these templates to change the number of premium data disks that are attached to the virtual machine.

* [Create VM with Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autobackup)
* [Create VM with Automated Patching](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-autopatching)
* [Create VM with AKV Integration](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-sql-full-keyvault)

### Quickstart template

You can use the following quickstart template to deploy a SQL Server VM using storage optimization.

* [Create VM with storage optimization](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage/)
* [Create VM using UltraSSD](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.sqlvirtualmachine/sql-vm-new-storage-ultrassd)

## Existing VMs

For existing SQL Server VMs, you can modify some storage settings in the Azure portal. Open your [SQL virtual machines resource](manage-sql-vm-portal.md#access-the-resource), and select **Overview**. The SQL Server **Overview** page shows the current storage usage of your VM. All drives that exist on your VM are displayed in this chart. For each drive, the storage space displays in four sections:

* SQL data
* SQL log
* Other (non-SQL storage)
* Available

To modify the storage settings, select **Storage configuration** under **Settings**.

![Screenshot that highlights the Configure option and the Storage Usage section.](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

You can modify the disk settings for the drives that were configured during the SQL Server VM creation process. Selecting **Configure** opens the drive modification page, allowing you to change the disk type, as well as add additional disks.

![Configure Storage for Existing SQL Server VM](./media/storage-configuration/sql-vm-storage-extend-drive.png)

You can also configure the settings for tempdb directly from the Azure portal, such as the number of data files, their intial size, and the autogrowth ratio. See [configure tempdb](manage-sql-vm-portal.md#storage) to learn more. 



## Automated changes

This section provides a reference for the storage configuration changes that Azure automatically performs during SQL Server VM provisioning or configuration in the Azure portal.

* Azure configures a storage pool from storage selected from your VM. The next section of this topic provides details about storage pool configuration.
* Automatic storage configuration always uses [premium SSDs](/azure/virtual-machines/disks-types) P30 data disks. Consequently, there is a 1:1 mapping between your selected number of Terabytes and the number of data disks attached to your VM.

For pricing information, see the [Storage pricing](https://azure.microsoft.com/pricing/details/storage) page on the **Disk Storage** tab.

### Creation of the storage pool

Azure uses the following settings to create the storage pool on SQL Server VMs.

| Setting | Value |
| --- | --- |
| Stripe size |256 KB (Data warehousing); 64 KB (Transactional) |
| Disk sizes |1 TB each |
| Cache |Read |
| Allocation size |64 KB NTFS allocation unit size |
| Recovery | Simple recovery (no resiliency) |
| Number of columns |Number of data disks up to 8<sup>1</sup> |


<sup>1</sup> After the storage pool is created, you cannot alter the number of columns in the storage pool.


### Workload optimization settings

The following table describes the three workload type options available and their corresponding optimizations:

| Workload type | Description | Optimizations |
| --- | --- | --- |
| **General** |Default setting that supports most workloads |None |
| **Transactional processing** |Optimizes the storage for traditional database OLTP workloads |Trace Flag 1117<br/>Trace Flag 1118 |
| **Data warehousing** |Optimizes the storage for analytic and reporting workloads |Trace Flag 610<br/>Trace Flag 1117 |

> [!NOTE]
> You can only specify the workload type when you provision a SQL Server virtual machine by selecting it in the storage configuration step.

## Enable caching

Change the caching policy at the disk level. You can do so using the Azure portal, [PowerShell](/powershell/module/az.compute/set-azvmdatadisk), or the [Azure CLI](/cli/azure/vm/disk).

To change your caching policy in the Azure portal, follow these steps:

1. Stop your SQL Server service.
1. Sign into the [Azure portal](https://portal.azure.com).
1. Navigate to your virtual machine, select **Disks** under **Settings**.

   ![Screenshot showing the VM disk configuration blade in the Azure portal.](./media/storage-configuration/disk-in-portal.png)

1. Choose the appropriate caching policy for your disk from the drop-down.

   ![Screenshot showing the disk caching policy configuration in the Azure portal.](./media/storage-configuration/azure-disk-config.png)

1. After the change takes effect, reboot the SQL Server VM and start the SQL Server service.


## Enable Write Accelerator

Write Acceleration is a disk feature that is only available for the M-Series Virtual Machines (VMs). The purpose of write acceleration is to improve the I/O latency of writes against Azure Premium Storage when you need single digit I/O latency due to high volume mission critical OLTP workloads or data warehouse environments.

Stop all SQL Server activity and shut down the SQL Server service before making changes to your write acceleration policy.

If your disks are striped, enable Write Acceleration for each disk individually, and your Azure VM should be shut down before making any changes.

To enable Write Acceleration using the Azure portal, follow these steps:

1. Stop your SQL Server service. If your disks are striped, shut down the virtual machine.
1. Sign into the [Azure portal](https://portal.azure.com).
1. Navigate to your virtual machine, select **Disks** under **Settings**.

   ![Screenshot showing the VM disk configuration blade in the Azure portal.](./media/storage-configuration/disk-in-portal.png)

1. Choose the cache option with **Write Accelerator** for your disk from the drop-down.

   ![Screenshot showing the write accelerator cache policy.](./media/storage-configuration/write-accelerator.png)

1. After the change takes effect, start the virtual machine and SQL Server service.

## Disk striping

For more throughput, you can add additional data disks and use disk striping. To determine the number of data disks, analyze the throughput and bandwidth required for your SQL Server data files, including the log and tempdb. Throughput and bandwidth limits vary by VM size. To learn more, see [VM Size](/azure/virtual-machines/sizes)


* For Windows 8/Windows Server 2012 or later, use [Storage Spaces](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)) with the following guidelines:

  1. Set the interleave (stripe size) to 64 KB (65,536 bytes) to avoid performance impact due to partition misalignment. This must be set with PowerShell.

  2. Set column count = number of physical disks. Use PowerShell when configuring more than 8 disks (not Server Manager UI).

For example, the following PowerShell creates a new storage pool with the interleave size to 64 KB and the number of columns equal to the amount of physical disk in the storage pool:

# [Windows Server 2016 +](#tab/windows2016)

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Windows Storage on <VM Name>" `
      -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks.Count -ResiliencySettingName simple `
      -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false
  ```

In Windows Server 2016 and later, the default value for `-StorageSubsystemFriendlyName` is `Windows Storage on <VM Name>`



# [Windows Server 2008 - 2012 R2](#tab/windows2012)



  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces on <VMName>" `
      -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks.Count -ResiliencySettingName simple `
      -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

In Windows Server 2008 to 2012 R2, the default value for `-StorageSubsystemFriendlyName` is `Storage Spaces on <VMName>`. 

---


  * For Windows 2008 R2 or earlier, you can use dynamic disks (OS striped volumes) and the stripe size is always 64 KB. This option is deprecated as of Windows 8/Windows Server 2012. For information, see the support statement at [Virtual Disk Service is transitioning to Windows Storage Management API](/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api).

  * If you are using [Storage Spaces Direct (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) with [SQL Server Failover Cluster Instances](./failover-cluster-instance-storage-spaces-direct-manually-configure.md), you must configure a single pool. Although different volumes can be created on that single pool, they will all share the same characteristics, such as the same caching policy.

  * Determine the number of disks associated with your storage pool based on your load expectations. Keep in mind that different VM sizes allow different numbers of attached data disks. For more information, see [Sizes for virtual machines](/azure/virtual-machines/sizes?toc=/azure/virtual-machines/windows/toc.json).


## Next steps

For other topics related to running SQL Server in Azure VMs, see [SQL Server on Azure Virtual Machines](sql-server-on-azure-vm-iaas-what-is-overview.md).