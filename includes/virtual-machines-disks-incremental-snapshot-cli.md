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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80343040"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

您可以使用 Azure CLI 創建增量快照，則需要最新版本的 Azure CLI。 

在 Windows 上，以下命令將安裝或更新現有安裝到最新版本：
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
在 Linux 上，CLI 安裝將因作業系統版本而異。  請參閱為特定 Linux 版本[安裝 Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli)

要創建增量快照，請使用使用[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)參數`--incremental`創建的 az 快照。

下面的示例`<yourDesiredSnapShotNameHere>`創建增量快照，替換 、`<yourResourceGroupNameHere>``<exampleDiskName>`和 使用`<exampleLocation>`您自己的值，然後運行示例：

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

您可以使用 快照`SourceResourceId`的屬性和快照`SourceUniqueId`的屬性標識同一磁片中的增量快照。 `SourceResourceId`是父磁片的 Azure 資源管理器資源識別碼。 `SourceUniqueId`是從磁片`UniqueId`屬性繼承的值。 如果要刪除磁片，然後創建同名的新磁片，`UniqueId`則屬性的值將更改。

可以使用`SourceResourceId`和`SourceUniqueId`創建與特定磁片關聯的所有快照的清單。 下面的示例將列出與特定磁片關聯的所有增量快照，但需要一些設置。

此示例使用 jq 查詢資料。 要運行該示例，必須[安裝 jq](https://stedolan.github.io/jq/download/)。

替換`<yourResourceGroupNameHere>`並使用`<exampleDiskName>`值替換和值，然後可以使用以下示例列出現有的增量快照，只要您也安裝了 jq：

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
