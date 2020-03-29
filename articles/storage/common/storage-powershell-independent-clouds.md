---
title: 使用 PowerShell 管理 Azure 獨立雲中的資料
titleSuffix: Azure Storage
description: 使用 Azure PowerShell 管理雲、政府雲和德國雲中的存儲。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895243"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>使用 PowerShell 在 Azure 獨立雲端中管理儲存體

大多數人會針對其全域 Azure 部署使用 Azure 公用雲端。 也有因為主權等等原因的一些獨立部署 Microsoft Azure。 這些獨立部署稱為「環境」。 下列清單詳細說明目前可用的獨立雲端。

* [Azure Government 雲端](https://azure.microsoft.com/features/gov/)
* [Azure中國 21Vianet 雲，由 21Vianet 在中國運營](http://www.windowsazure.cn/)
* [Azure 德國雲端](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>使用獨立雲端

若要在其中一個獨立雲端中使用 Azure 儲存體，您要連線到該雲端，而非 Azure 公用。 若要使用其中一個獨立雲端，而非 Azure 公用：

* 指定要連線的「環境」**。
* 決定並且使用可用的區域。
* 使用正確的端點尾碼，它與 Azure 公用的尾碼不同。

這些範例需要 Azure PowerShell 模組 Az 0.7 版或更新版本。 在 PowerShell 視窗中，執行 `Get-Module -ListAvailable Az` 以尋找版本。 如果未列出任何項目，或者您必須升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-Az-ps)。

## <a name="log-in-to-azure"></a>登入 Azure

執行 [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) Cmdlet，查看可用的 Azure 環境：

```powershell
Get-AzEnvironment
```

登入您的帳戶，該帳戶可存取您想要連線並且設定環境的雲端。 這個範例示範如何登入使用 Azure Government 雲端的帳戶。   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

若要存取中國雲端，請使用環境 **AzureChinaCloud**。 若要存取德國雲端，請使用 **AzureGermanCloud**。

此時，如果您需要用來建立儲存體帳戶或其他資源的位置清單，您可以使用 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) 查詢適用於選取之雲端的位置。

```powershell
Get-AzLocation | select Location, DisplayName
```

下表顯示針對德國雲端傳回的位置。

|Location | 顯示名稱 |
|----|----|
| `germanycentral` | 德國中部|
| `germanynortheast` | 德國東北部 |


## <a name="endpoint-suffix"></a>端點尾碼

端點尾碼，這些環境的每個尾碼都與 Azure 公用端點不同。 例如，Azure 公用的 blob 端點尾碼是 **blob.core.windows.net**。 針對政府雲端，blob 端點尾碼是 **blob.core.usgovcloudapi.net**。

### <a name="get-endpoint-using-get-azenvironment"></a>使用 Get-AzEnvironment 取得端點

使用 [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) 擷取端點尾碼。 端點是環境的「StorageEndpointSuffix」** 屬性。

以下程式碼片段演示如何檢索終結點尾碼。 所有這些命令都會返回諸如"core.cloudapp.net"或"core.cloudapi.de"等內容。將尾碼追加到存儲服務以訪問該服務。 例如，"queue.core.cloudapi.de" 會在德國雲端中存取佇列服務。

此程式碼片段會擷取所有環境及其端點尾碼。

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

此命令會傳回下列結果。

| 名稱| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

若要擷取指定環境的所有屬性，請呼叫 **Get-AzEnvironment** 並且指定雲端名稱。 此程式碼片段會傳回屬性清單。在清單中尋找 **StorageEndpointSuffix**。 下列範例適用於德國雲端。

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

結果類似于以下值：

|屬性名稱|值|
|----|----|
| 名稱 | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
若只要擷取儲存體端點尾碼屬性，請擷取特定雲端，並且只要求該單一屬性。

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

此命令會傳回下列資訊：

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>從儲存體帳戶取得端點

您還可以檢查存儲帳戶的屬性以檢索終結點：

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

對於政府雲中的存儲帳戶，此命令返回以下輸出：

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>設定環境之後

從這裡開始，您可以使用與用來管理儲存體帳戶及存取資料層的相同 PowerShell，如同[使用 Azure PowerShell 與 Azure 儲存體](storage-powershell-guide-full.md)一文所述。

## <a name="clean-up-resources"></a>清除資源

如果為此練習創建了一個新資源組和存儲帳戶，則可以通過刪除資源組來刪除這兩個資產。 刪除資源群組會刪除群組內含的所有資源。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

* [在 PowerShell 工作階段之間保存使用者登入](/powershell/azure/context-persistence)
* [Azure 政府存儲](../../azure-government/documentation-government-services-storage.md)
* [Microsoft Azure Government 開發人員指南](../../azure-government/documentation-government-developer-guide.md)
* [Azure 中國 21Vianet 應用程式的開發人員說明](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure 德國文件](../../germany/germany-welcome.md)
