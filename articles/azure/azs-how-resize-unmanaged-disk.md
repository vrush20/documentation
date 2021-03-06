---
title: How to resize an unmanaged disk - PowerShell | UKCloud Ltd
description: Provides help for resizing an unmanaged disk on UKCloud for Microsoft Azure
services: azure-stack
author: Bailey Lawson
toc_rootlink: Users
toc_sub1: How To
toc_sub2:
toc_sub3:
toc_sub4:
toc_title: Resize an unmanaged disk - PowerShell
toc_fullpath: Users/How To/azs-how-resize-unmanaged-disk.md
toc_mdlink: azs-how-resize-unmanaged-disk.md
---

# How to resize an unmanaged disk - PowerShell

## Overview

When deploying a new virtual machine from an Azure Marketplace image, the default drive size is often 127GB or less. While it is recommended to add additional disks for tasks such as installing applications and for CPU intensive workloads, you may need to expand the initial drive for purposes such as migrating from a physical PC to the VM or to support applications that must be installed on the OS drive.

This guide shows you how to resize disks on UKCloud for Microsoft Azure.

> [!NOTE]
> Resizing a disk will cause the virtual machine to restart.

### Intended audience

To complete the steps in this guide you must have the appropriate permissions on the resource you are trying to access.

## Resizing an unmanaged disk

From your PowerShell window:

> [!IMPORTANT]
> Enter details below to provide values for the variables in the scripts in this article:
>
> Resource Group Name: <form oninput="result.value=resourcegroup.value" id="resourcegroup" style="display: inline;" >
> <input  type="text" id="resourcegroup" name="resourcegroup" style="display: inline;"/></form>
>
> VM Name: <form oninput="result.value=vmname.value" id="vmname" style="display: inline;">
> <input  type="text" id="vmname" name="vmname" style="display: inline;"/></form>
>
> New Disk Size in GB: <form oninput="result.value=disksize.value" id="disksize" style="display: inline;">
> <input  type="text" id="disksize" name="disksize" style="display: inline;"/></form>
>
> Note that the maximum size allowed for OS disks is 2048GB

<pre><code class="language-PowerShell"># Sign in to your Azure Active Directory account in resource management mode
Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.frn00006.azure.ukcloud.com"
Login-AzureRmAccount -EnvironmentName "AzureStackUser"

# Set your resource group name and VM name
$RGName = '<output form="resourcegroup" name="result" style="display: inline;">&lt;Resource Group&gt;</output>'
$VMName = '<output form="vmname" name="result" style="display: inline;">&lt;VM Name&gt;</output>'

# Obtain a reference to your VM
$VM = Get-AzureRmVM -ResourceGroupName $RGName -Name $VMName

# Stop the VM before resizing the disk
Stop-AzureRmVM -ResourceGroupName $RGName -Name $VMName

# Set the size of the unmanaged OS disk to the desired value and update the VM
$VM.StorageProfile.OSDisk.DiskSizeGB = <output form="disksize" name="result" style="display: inline;">&lt;Disk Size&gt;</output>
Update-AzureRmVM -ResourceGroupName $RGName -VM $VM

# Restart the VM
Start-AzureRmVM -ResourceGroupName $RGName -Name $VMName
</code></pre>

## Expanding the volume

After expanding the disk, you must go into the OS and expand the volume to actually use the newly allocated space. To do so follow these steps:

# [Windows VM](#tab/tabid-1)

1. Open an RDP connection to your VM.

2. Open a command prompt and type `diskpart`.

3. At the `DISKPART` prompt, type `list volume`. Take note of the volume you want to extend.

4. At the `DISKPART` prompt, type `select volume <volumenumber>`. This selects the volume that you want to extend into unpartitioned empty space on the same disk.

5. At the `DISKPART` prompt, type `extend`. This extends the selected volume to fill the added space on the disk.

# [Linux VM](#tab/tabid-2)

No further action is required.

***

## Feedback

If you have any comments on this document or any other aspect of your UKCloud experience, send them to <products@ukcloud.com>.
