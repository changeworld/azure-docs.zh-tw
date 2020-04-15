---
title: PowerShell 指令碼範例
description: 示範如何透過 PowerShell 指令碼使用前端的範例
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891487"
---
# <a name="example-powershell-scripts"></a>PowerShell 指令碼範例

Azure 遠端轉譯提供下列兩個 REST API：

- [轉換 REST API](../how-tos/conversion/conversion-rest-api.md)
- [工作階段 REST API](../how-tos/session-rest-api.md)

[ARR 範例存放庫](https://github.com/Azure/azure-remote-rendering)的 *Scripts* 資料夾中包含範例指令碼，可供與服務的 REST API 互動。 本文說明其使用方式。

## <a name="prerequisites"></a>Prerequisites

若要執行範例指令碼，您需要 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 的功能設定。

1. 安裝 Azure PowerShell：
    1. 以系統管理員權限開啟 PowerShell
    1. 執行：`Install-Module -Name Az -AllowClobber`

1. 如果您收到有關執行指令碼的錯誤，請確定已適當設定[執行原則](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)：
    1. 以系統管理員權限開啟 PowerShell
    1. 執行：`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [準備 Azure 儲存體帳戶](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. 登入含有 Azure 遠端轉譯帳戶的訂用帳戶：
    1. 開啟 PowerShell
    1. 執行：`Connect-AzAccount`並遵循螢幕上的指示操作。

> [!NOTE]
> 如果您的組織有多個訂用帳戶，您可能需要指定 SubscriptionId 和 Tenant 引數。 在 [Connect-AzAccount documentation](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) 中尋找詳細資料。

1. 從 [Azure 遠端轉譯 GithHub 存放庫](https://github.com/Azure/azure-remote-rendering)下載 *Scripts* 資料夾。

## <a name="configuration-file"></a>組態檔

在 `.ps1` 檔案旁邊，有一個您需要填妥的 `arrconfig.json`：

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> 請務必使用雙反斜線，在 LocalAssetDirectoryPath 路徑中正確地逸出反斜線："\\\\"，並在所有其他路徑 (例如 inputFolderPath 和 inputAssetPath) 中使用正斜線 "/"。

### <a name="accountsettings"></a>accountSettings

對於 `arrAccountId` 和 `arrAccountKey`，請參閱[建立 Azure 遠端轉譯帳戶](../how-tos/create-an-account.md)。
對於 `region`，請參閱[可用區域清單](../reference/regions.md)。

### <a name="renderingsessionsettings"></a>renderingSessionSettings

如果您想要執行 **RenderingSession.ps1**，就必須填妥此結構。

- **vmSize：** 選取虛擬機器的大小。 選取「標準」  或「進階」  。 當您不再需要轉譯工作階段時，請將其關閉。
- **maxLeaseTime：** 您要租用 VM 的持續時間。 當租用到期時，工作階段就會關閉。 稍後可以延長的租用時間 (請參閱下文)。

### <a name="assetconversionsettings"></a>assetConversionSettings

如果您想要執行 **Conversion.ps1**，就必須填妥此結構。

如需詳細資訊，請參閱[準備 Azure 儲存體帳戶](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)。

## <a name="script-renderingsessionps1"></a>指令碼：RenderingSession.ps1

此指令碼用來建立、查詢和停止轉譯工作階段。

> [!IMPORTANT]
> 請確定您已填妥 arrconfig.json 中的 accountSettings  和 renderingSessionSettings  區段。

### <a name="create-a-rendering-session"></a>建立轉譯工作階段

已完全填妥的 arrconfig.json 一般用法：

```PowerShell
.\RenderingSession.ps1
```

此指令碼將會呼叫[工作階段管理 REST API](../how-tos/session-rest-api.md)，以使用指定的設定來加速轉譯 VM。 成功時，其會取得 *sessionId*。 然後，其會輪詢工作階段屬性，直到工作階段準備就緒或發生錯誤為止。

若要使用**替代組態**檔案：

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

您可以**覆寫組態檔中的個別設定檔**：

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

若只要**啟動工作階段但不輪詢**，您可使用：

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

指令碼所擷取的 *sessionId* 必須傳遞至大部分其他的工作階段命令。

### <a name="retrieve-session-properties"></a>設定工作階段屬性

若要取得工作階段的屬性，請執行：

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

使用 `-Poll` 以等到工作階段已「準備就緒」  或發生錯誤。

### <a name="list-active-sessions"></a>列出作用中的工作階段

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>停止工作階段

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>變更工作階段屬性

目前，我們只支援變更工作階段的 maxLeaseTime。

> [!NOTE]
> 租用時間一律會從工作階段 VM 最初建立的時間算起。 因此，若要將工作階段租用延長一小時，請將 *maxLeaseTime* 增加一小時。

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>指令碼：Conversion.ps1

此指令碼用於將輸入模型轉換成 Azure 遠端轉譯的特定執行階段格式。

> [!IMPORTANT]
> 請確定您已填妥 arrconfig.json 中的 accountSettings  和 assetConversionSettings  區段。

此指令碼示範使用儲存體帳戶搭配服務的兩個選項：

- 與 Azure 遠端轉譯帳戶連結的儲存體帳戶
- 可透過共用存取簽章 (SAS) 存取儲存體

### <a name="linked-storage-account"></a>連結的儲存體帳戶

在您完全填妥 arrconfig.json 並連結儲存體帳戶後，您即可使用下列命令。 如需連結儲存體帳戶的詳細說明，請參閱[建立帳戶](../how-tos/create-an-account.md#link-storage-accounts)。

使用連結的儲存體帳戶是使用轉換服務的慣用方式，因為不需要產生共用存取簽章。

```PowerShell
.\Conversion.ps1
```

1. 將 `assetConversionSettings.modelLocation` 內含的所有檔案上傳至指定的 `inputFolderPath` 之下的輸入 Blob 容器
1. 呼叫[模型轉換 REST API](../how-tos/conversion/conversion-rest-api.md)以開始進行[模型轉換](../how-tos/conversion/model-conversion.md)
1. 輪詢轉換狀態，直到轉換成功或失敗為止
1. 輸出已轉換檔案位置的詳細資料 (儲存體帳戶、輸出容器、容器中的檔案路徑)

### <a name="access-to-storage-via-shared-access-signatures"></a>透過共用存取簽章存取儲存體

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

這將會：

1. 將本機檔案從 `assetConversionSettings.localAssetDirectoryPath` 上傳至輸入 Blob 容器
1. 產生輸入容器的 SAS URI
1. 產生輸出容器的 SAS URI
1. 呼叫[模型轉換 REST API](../how-tos/conversion/conversion-rest-api.md)以開始進行[模型轉換](../how-tos/conversion/model-conversion.md)
1. 輪詢轉換狀態，直到轉換成功或失敗為止
1. 輸出已轉換檔案位置的詳細資料 (儲存體帳戶、輸出容器、容器中的檔案路徑)
1. 將 SAS URI 輸出至輸出 Blob 容器中已轉換的模型

### <a name="additional-command-line-options"></a>其他命令列選項

若要使用**替代組態**檔案：

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

若只要**啟動模型轉換但不輪詢**，您可使用：

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

您可使用下列命令列參數，**覆寫組態檔中的個別設定**：

* **Id：** 搭配 GetConversionStatus 使用的 ConversionId
* **ArrAccountId：** accountSettings 的 arrAccountId
* **ArrAccountKey：** 覆寫 accountSettings 的 arrAccountKey
* **Region：** 覆寫 accountSettings 的 region
* **ResourceGroup：** 覆寫 assetConversionSettings 的 resourceGroup
* **StorageAccountName：** 覆寫 assetConversionSettings 的 storageAccountName
* **BlobInputContainerName：** 覆寫 assetConversionSettings 的 blobInputContainer
* **LocalAssetDirectoryPath：** 覆寫 assetConversionSettings 的 localAssetDirectoryPath
* **InputAssetPath：** 覆寫 assetConversionSettings 的 inputAssetPath
* **：** 覆寫 assetConversionSettings 的 blobOutputContainerName
* **OutputFolderPath：** 覆寫 assetConversionSettings 的 outputFolderPath
* **OutputAssetFileName：** 覆寫 assetConversionSettings 的 outputAssetFileName

例如，您可以結合數個指定的選項，如下所示：

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>執行個別轉換階段

如果您想要執行程序的個別步驟，您可使用：

只從指定的 LocalAssetDirectoryPath 上傳資料

```PowerShell
.\Conversion.ps1 -Upload
```

只啟動已上傳至 Blob 儲存體的模型轉換程序 (不要執行上傳，不要輪詢轉換狀態)。指令碼會傳回 *conversionId*。

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

而您可使用以下項目來擷取此轉換的轉換狀態：

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

使用 `-Poll` 以等到轉換完成或發生錯誤。

## <a name="next-steps"></a>後續步驟

- [快速入門：使用 Unity 轉譯模型](../quickstarts/render-model.md)
- [快速入門：轉換模型以進行轉譯](../quickstarts/convert-model.md)
- [模型轉換](../how-tos/conversion/model-conversion.md)
