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
ms.openlocfilehash: 69be71a58c3aed4f52b77e63c9ddf12365301b08
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299146"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>支援區域
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

您可以使用 Azure CLI 建立增量快照集，您將需要最新版的 Azure CLI。 

在 Windows 中，下列命令會將您現有的安裝安裝或更新為最新版本：
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
在 Linux 上，CLI 安裝會根據作業系統版本而有所不同。  請參閱安裝特定 Linux 版本[的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 。

若要建立增量快照集，請使用[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create)搭配 `--incremental` 參數。

下列範例會建立增量快照集、以您自己的值取代 `<yourDesiredSnapShotNameHere>`、`<yourResourceGroupNameHere>`、`<exampleDiskName>`和 `<exampleLocation>`，然後執行範例：

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

您可以使用 `SourceResourceId` 和快照的 `SourceUniqueId` 屬性，從相同的磁片識別增量快照集。 `SourceResourceId` 是父磁片的 Azure Resource Manager 資源識別碼。 `SourceUniqueId` 是從磁片的 `UniqueId` 屬性繼承而來的值。 如果您要刪除磁片，然後使用相同的名稱建立新的磁片，`UniqueId` 屬性的值就會變更。

您可以使用 `SourceResourceId` 和 `SourceUniqueId`，建立與特定磁片相關聯的所有快照集清單。 下列範例會列出與特定磁片相關聯的所有增量快照集，但它需要進行一些設定。

這個範例會使用 jq 來查詢資料。 若要執行範例，您必須[安裝 jq](https://stedolan.github.io/jq/download/)。

以您的值取代 `<yourResourceGroupNameHere>` 和 `<exampleDiskName>`，然後您就可以使用下列範例來列出現有的增量快照集，只要您已安裝 jq：

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
```

## <a name="resource-manager-template"></a>Resource Manager 範本

您也可以使用 Azure Resource Manager 範本來建立增量快照集。 您必須確定 apiVersion 設定為**2019-03-01** ，而且累加屬性也設定為 true。 下列程式碼片段是如何使用 Resource Manager 範本建立增量快照集的範例：

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

如果您想要查看示範增量快照集差異功能的範例程式碼，請使用 .NET，請參閱[將 Azure 受控磁碟備份複製到具有增量快照差異功能的另一個區域](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)。
