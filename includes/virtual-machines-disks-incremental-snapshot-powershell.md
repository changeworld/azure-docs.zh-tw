---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 895a81df858e51a266cd87c96a161695a4bf2cc1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95992909"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>支援區域
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 來建立增量快照集。 您將需要最新版的 Azure PowerShell，下列命令會安裝它，或將您現有的安裝更新為最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

一旦安裝之後，請使用來登入您的 PowerShell 會話 `Connect-AzAccount` 。

若要建立具有 Azure PowerShell 的累加式快照集，請使用 [AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) 搭配參數設定設定， `-Incremental` 然後透過參數將其作為變數傳遞至 [>new-azsnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) `-Snapshot` 。

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

您可以使用和快照集的屬性來識別相同磁片的增量快照集 `SourceResourceId` `SourceUniqueId` 。 `SourceResourceId` 這是父磁片的 Azure Resource Manager 資源識別碼。 `SourceUniqueId` 這是從磁片的屬性繼承而來的值 `UniqueId` 。 如果您要刪除磁片，然後使用相同的名稱建立新的磁片，屬性的值會 `UniqueId` 變更。

您可以使用 `SourceResourceId` 和 `SourceUniqueId` 來建立與特定磁片相關聯之所有快照集的清單。 `<yourResourceGroupNameHere>`以您的值取代，然後您可以使用下列範例來列出現有的增量快照：

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager 範本

您也可以使用 Azure Resource Manager 範本來建立增量快照集。 您必須確認 apiVersion 設定為 **2019-03-01** ，而且累加屬性也設定為 true。 下列程式碼片段是如何使用 Resource Manager 範本建立累加式快照集的範例：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>後續步驟

如果您想要查看使用 .NET 來示範增量快照差異功能的範例程式碼，請參閱 [將 Azure 受控磁碟備份複製到具有增量快照差異功能的另一個區域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。