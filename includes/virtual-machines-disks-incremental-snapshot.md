---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 12/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2316b52115cc23d11dfb26d951a7f1820f2737cc
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945840"
---
增量快照集（預覽）是受控磁片的時間點備份，在這種情況下，只會包含自上一個快照集之後的所有變更。 當您嘗試下載或使用增量快照集時，會使用完整的 VHD。 這項適用于受控磁片快照集的新功能可能會讓它們更符合成本效益，因為您不再需要將整個磁片與每個個別的快照集一起儲存，除非您選擇。 就像一般快照集一樣，您可以使用增量快照集來建立完整的受控磁片，或建立一般的快照集。

增量快照集和一般快照集之間有一些差異。 增量快照集會一律使用標準 Hdd 存放裝置，而不考慮磁片的儲存體類型，而一般快照集可以使用 premium Ssd。 如果您在進階儲存體上使用一般快照集來相應增加 VM 部署，建議您在[共用映射資源庫](../articles/virtual-machines/linux/shared-image-galleries.md)中的標準儲存體上使用自訂映射。 它可協助您以較低的成本達成更大規模的規模。 此外，增量快照集可能會透過[區域冗余儲存體](../articles/storage/common/storage-redundancy-zrs.md)（ZRS）提供更好的可靠性。 如果選取的區域中有可用的 ZRS，則增量快照集會自動使用 ZRS。 如果 ZRS 無法在區域中使用，則快照集會預設為[本機多餘的儲存體](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以覆寫此行為，並手動選取一個，但我們不建議這麼做。

增量快照集也提供差異功能，這是受控磁片的唯一可用性。 它們可讓您將相同受控磁片的兩個增量快照集之間的變更，減少到區塊層級。 您可以使用這項功能來減少跨區域複製快照集時的資料使用量。

### <a name="supported-regions"></a>支援區域

目前僅支援下欄區域：

- 可在美國中西部、美國東部、美國東部2、美國中部、加拿大東部、加拿大中部、北歐、南部東亞區域中正式推出。

## <a name="restrictions"></a>限制
- 在您變更磁片大小（僅限預覽期間）時，目前無法建立增量快照集。
- 目前無法在訂用帳戶之間移動增量快照集。
- 您目前最多隻能在任何指定的時間產生特定快照集系列的五個快照集的 SAS Uri。
- 您無法為該磁片的訂用帳戶以外的特定磁片建立增量快照集。
- 每個磁片最多可以建立7個增量快照集每五分鐘一次。
- 總共可以針對單一磁片建立總計200的增量快照集。

## <a name="powershell"></a>PowerShell

您可以使用 Azure PowerShell 來建立增量快照集。 您將需要最新版的 Azure PowerShell，下列命令會安裝它，或將現有的安裝更新為最新版本：

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

一旦安裝完成後，請使用 `az login`登入您的 PowerShell 會話。

若要使用 Azure PowerShell 建立增量快照集，請使用[AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0)搭配 `-Incremental` 參數來設定設定，然後透過 `-Snapshot` 參數將其當做變數傳遞至[new-azsnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) 。

以您的值取代 `<yourDiskNameHere>`、`<yourResourceGroupNameHere>`和 `<yourDesiredSnapShotNameHere>`，然後您就可以使用下列腳本來建立增量快照集：

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

您可以使用 `SourceResourceId` 和快照的 `SourceUniqueId` 屬性，從相同的磁片識別增量快照集。 `SourceResourceId` 是父磁片的 Azure Resource Manager 資源識別碼。 `SourceUniqueId` 是從磁片的 `UniqueId` 屬性繼承而來的值。 如果您要刪除磁片，然後使用相同的名稱建立新的磁片，`UniqueId` 屬性的值就會變更。

您可以使用 `SourceResourceId` 和 `SourceUniqueId`，建立與特定磁片相關聯的所有快照集清單。 將 `<yourResourceGroupNameHere>` 取代為您的值，然後您可以使用下列範例來列出現有的增量快照集：

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
