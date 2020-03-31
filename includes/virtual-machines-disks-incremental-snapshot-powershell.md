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
ms.openlocfilehash: 3eec6583ebdff35d7e40d2eec305a947de0cb87c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299445"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>支援區域
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 創建增量快照。 您將需要最新版本的 Azure PowerShell，以下命令將安裝它或更新現有安裝到最新：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

安裝後，使用`az login`登錄到 PowerShell 會話。

要使用 Azure PowerShell 創建增量快照，請使用`-Incremental`該參數設置[使用 New-AzSnapConfig 的](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0)配置，然後將該配置作為變數傳遞到[New-AzSnapshot。](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) `-Snapshot`

使用`<yourDiskNameHere>``<yourResourceGroupNameHere>`值替換`<yourDesiredSnapShotNameHere>`和 ，然後可以使用以下腳本創建增量快照：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

您可以使用 快照`SourceResourceId`的屬性和快照`SourceUniqueId`的屬性標識同一磁片中的增量快照。 `SourceResourceId`是父磁片的 Azure 資源管理器資源識別碼。 `SourceUniqueId`是從磁片`UniqueId`屬性繼承的值。 如果要刪除磁片，然後創建同名的新磁片，`UniqueId`則屬性的值將更改。

可以使用`SourceResourceId`和`SourceUniqueId`創建與特定磁片關聯的所有快照的清單。 替換為`<yourResourceGroupNameHere>`值，然後可以使用以下示例列出現有的增量快照：

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

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

您還可以使用 Azure 資源管理器範本創建增量快照。 您需要確保 apiVersion 設置為**2019-03-01，** 並且增量屬性也設置為 true。 以下程式碼片段是如何使用資源管理器範本創建增量快照的示例：

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

如果要查看示例代碼，演示使用 .NET 的增量快照的差分功能，請參閱[將 Azure 託管磁片備份複製到具有增量快照差異功能的另一個區域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
