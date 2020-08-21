---
title: Azure Key Vault 記錄 | Microsoft Docs
description: 要了解如何監視金鑰保存庫存取，您可以啟用 Azure Key Vault 記錄，以在您提供的 Azure 儲存體帳戶中儲存這方面的資訊。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: e9507525dc2c52f584bd7883a12da401b5999f50
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585911"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 記錄

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在建立一或多個金鑰保存庫之後，您可能會想要監視金鑰保存庫的存取方式、時間和存取者。 想要做到這點，您可以啟用 Azure Key Vault 記錄，以在您提供的 Azure 儲存體帳戶中儲存這方面的資訊。 系統會自動為您指定的儲存體帳戶建立名為 **insights-logs-auditevent** 的新容器。 您可以使用此相同儲存體帳戶來收集多個金鑰保存庫的記錄。

在金鑰保存庫作業 10 分鐘 (最多) 後，您就可以存取記錄資訊。 但大多不用這麼久。  儲存體帳戶中的記錄由您全權管理：

* 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
* 刪除不想繼續保留在儲存體帳戶中的記錄。

使用本教學課程來協助您開始使用 Azure 金鑰保存庫記錄。 您將會建立儲存體帳戶、啟用記錄及解譯收集到的記錄資訊。  

> [!NOTE]
> 本教學課程不會指示如何建立金鑰保存庫、金鑰或密碼。 如需此資訊，請參閱[什麼是 Azure Key Vault？](overview.md))。 或者，如需跨平台 Azure CLI 的指示，請參閱[這個對等的教學課程](manage-with-cli2.md))。
>
> 本文提供適用於更新診斷記錄的 Azure PowerShell 指示。 您也可以使用 Azure 監視器，在Azure 入口網站的 [診斷記錄] 區段中更新診斷記錄。 
>

如 Key Vault 的概觀資訊，請參閱[什麼是 Azure Key Vault？](overview.md))。 如需 Key Vault 適用地區的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，必須具備下列項目：

* 所使用的現有金鑰保存庫。  
* Azure PowerShell，最低版本為 1.0.0。 若要安裝 Azure PowerShell，並將它與 Azure 訂用帳戶建立關聯，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/)。 如果您已安裝 Azure PowerShell 但不知道版本，請在 Azure PowerShell 主控台中輸入 `$PSVersionTable.PSVersion`。  
* 足夠的 Azure 儲存體以儲存金鑰保存庫記錄。

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>連線至金鑰保存庫訂用帳戶

設定金鑰記錄的第一個步驟是將 Azure PowerShell 指向您想要記錄的金鑰保存庫。

使用下列命令開始 Azure PowerShell 工作階段，並登入您的 Azure 帳戶：  

```powershell
Connect-AzAccount
```

在快顯瀏覽器視窗中，輸入您的 Azure 帳戶使用者名稱與密碼。 Azure PowerShell 會取得與此帳戶相關聯的所有訂用帳戶。 根據預設，PowerShell 會使用第一個訂用帳戶。

您可能必須指定用來建立金鑰保存庫的訂用帳戶。 輸入下列命令以查看您帳戶的訂用帳戶：

```powershell
Get-AzSubscription
```

接著，若要指定訂用帳戶來與要記錄的金鑰保存庫建立關聯，請輸入：

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

將 PowerShell 指向正確訂用帳戶的步驟很重要，特別是當您有多個與帳戶相關聯的訂用帳戶時。 如需設定 Azure PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/)。

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>為您的記錄建立儲存體帳戶

雖然您可以使用現有儲存體帳戶來儲存記錄，但我們將建立儲存體帳戶來專用儲存金鑰保存庫記錄。 為了方便起見，在稍後遇到必須指定此帳戶時，我們會將詳細資料儲存到名為 **sa** 的變數中。

為了進一步簡化管理，我們也會使用包含金鑰保存庫的同一個資源群組。 在 [開始使用教學課程](../secrets/quick-create-cli.md)中，此資源群組的名稱是 **ContosoResourceGroup**，並且我們會繼續使用「東亞」位置。 請視情況將這些值替換成您自己的值：

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> 如果您決定使用現有儲存體帳戶，該帳戶必須使用與金鑰保存庫相同的訂用帳戶。 而且也必須使用 Azure Resource Manager 部署模型，而非傳統部署模型。
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>識別用來儲存記錄的金鑰保存庫

在[快速入門教學課程](../secrets/quick-create-cli.md)中，金鑰保存庫的名稱為 **ContosoKeyVault**。 我們會繼續使用該名稱，並將詳細資料儲存在名為 **kv** 的變數中：

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging-using-azure-powershell"></a><a id="enable"></a>使用 Azure PowerShell 啟用記錄

為了啟用 Key Vault 記錄，我們將使用 **Set-AzDiagnosticSetting** Cmdlet，並搭配針對新儲存體帳戶和金鑰保存庫所建立的變數。 我們也會將 **-Enabled** 旗標設定為 **$true**，並將類別設定為 `AuditEvent` (Key Vault 記錄唯一適用的類別)：

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

輸出如下所示：

```output
StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccountContosoKeyVaultLogs
ServiceBusRuleId   :
StorageAccountName :
    Logs
    Enabled           : True
    Category          : AuditEvent
    RetentionPolicy
    Enabled : False
    Days    : 0
```

此輸出可確認金鑰保存庫記錄現已啟用，而這會將資訊儲存到儲存體帳戶中。

您可以選擇性地設定記錄的保留原則，以便自動刪除較舊的記錄。 例如，藉由設定 **-RetentionEnabled** 旗標將保留原則設為 **$true** 並將 **-RetentionInDays** 參數設為 **90**，以便自動刪除超過 90 天的舊記錄。

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

所記錄的內容：

* 所有已驗證的 REST API 要求，包括因為存取權限、系統錯誤或要求錯誤而發生的失敗要求。
* 對金鑰保存庫本身所執行的作業，包括建立、刪除、設定金鑰保存庫存取原則，以及更新金鑰保存庫屬性 (例如標記)。
* 金鑰保存庫中的金鑰和祕密作業，包括：
  * 建立、修改或刪除這些金鑰或祕密。
  * 簽署、驗證、加密、解密、包裝和解除包裝金鑰、取得秘密，以及列出金鑰和祕密 (及其版本)。
* 產生 401 回應的未經驗證要求。 例如，沒有持有人權杖的要求、格式不正確或已過期的要求，或具有無效權杖的要求。  

## <a name="enable-logging-using-azure-cli"></a>使用 Azure CLI 啟用記錄

```azurecli
az login

az account set --subscription {AZURE SUBSCRIPTION ID}

az provider register -n Microsoft.KeyVault

az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="access-your-logs"></a><a id="access"></a>存取記錄

Key Vault 記錄儲存在所提供儲存體帳戶的 **insights-logs-auditevent** 容器中。 若要查看記錄，您必須下載 Blob。

首先，建立容器名稱的變數。 您將在本逐步解說的其餘部分使用此變數。

```powershell
$container = 'insights-logs-auditevent'
```

若要列出此容器中的所有 Blob，請輸入：

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

輸出大致如下：

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

在此輸出中我們可以看到，blob 遵循以下命名慣例：`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

日期和時間值使用 UTC。

因為您可以使用相同的儲存體帳戶收集多個資源的記錄，所以 Blob 名稱中的完整資源識別碼適合用來只存取或下載所需 Blob。 但在這麼做之前，我們要先討論如何下載所有 blob。

建立資料夾來下載 Blob。 例如：

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

然後取得所有 blob 的清單：  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

透過 **Get-AzStorageBlobContent** 以管道傳送這份清單，將 Blob 下載到目的地資料夾：

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

在執行第二個命令時，blob 名稱中的 **/** 分隔符號會在目的地資料夾下建立完整資料夾結構。 您將使用此結構來下載 Blob 並儲存為檔案。

若要有所選擇地下載 blob，請使用萬用字元。 例如：

* 如果您有多個金鑰保存庫，並且只想下載其中的 CONTOSOKEYVAULT3 金鑰保存庫的記錄：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* 如果您有多個資源群組，並且只想下載其中某個資源群組的記錄，請使用 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* 如果您想下載 2019 年 1 月份當月的所有記錄，請使用 `-Blob '*/year=2019/m=01/*'`：

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

您現在已做好準備，可以開始查看記錄中有何內容。 但是在繼續進行之前，您應該多了解兩個命令：

* 若要查詢金鑰保存庫資源的診斷設定狀態：`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* 若要停用金鑰保存庫資源記錄： `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`


## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>解譯金鑰保存庫記錄

各個 blob 皆會儲存為文字，並格式化為 JSON blob。 讓我們看看記錄項目範例。 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出各個欄位的名稱和其描述：

| 欄位名稱 | 描述 |
| --- | --- |
| **time** |日期和時間 (UTC)。 |
| **resourceId** |Azure Resource Manager 資源識別碼。 對於金鑰保存庫記錄來說，這一律是金鑰保存庫的資源識別碼。 |
| **operationName** |下一份表格所述作業的名稱。 |
| **operationVersion** |用戶端所要求的 REST API 版本。 |
| **類別** |結果的類型。 對於 Key Vault 記錄來說，`AuditEvent` 是其唯一可用的值。 |
| **resultType** |REST API 要求的結果。 |
| **resultSignature** |HTTP 狀態。 |
| **resultDescription** |結果的其他描述 (若有提供)。 |
| **durationMs** |服務 REST API 要求時所花費的時間，以毫秒為單位。 此時間不包括網路延遲，因此在用戶端所測量到的時間可能不符合此時間。 |
| **callerIpAddress** |提出要求用戶端的 IP 位址。 |
| **correlationId** |選擇性的 GUID，用戶端可傳遞此 GUID 來讓用戶端記錄與服務端 (金鑰保存庫) 記錄相互關聯。 |
| **身分識別** |權杖中的身分識別，會在 REST API 要求中提供。 就像從 Azure PowerShell Cmdlet 產生的要求一樣，這通常是「使用者」、「服務主體」或「使用者+appId」的組合。 |
| **properties** |根據作業 (**operationName**) 而有所不同的資訊。 在大部分情況下，此欄位會包含用戶端資訊 (用戶端傳遞的使用者代理程式字串)、完全符合的 REST API 要求 URI 和 HTTP 狀態碼。 此外，在因為提出要求 (例如，**KeyCreate** 或 **VaultGet**) 而傳回物件時，此欄位也會包含金鑰 URI (形式為 `id`)、保存庫 URI 或祕密 URI。 |

**operationName** 欄位值的格式為 *ObjectVerb*。 例如：

* 所有金鑰保存庫作業都有 `Vault<action>` 格式，例如 `VaultGet` 和 `VaultCreate`。
* 所有金鑰作業都有 `Key<action>` 格式，例如 `KeySign` 和 `KeyList`。
* 所有祕密作業都有 `Secret<action>` 格式，例如 `SecretGet` 和 `SecretListVersions`。

下表列出 **operationName** 值和相對應的 REST API 命令：

| operationName | REST API 命令 |
| --- | --- |
| **驗證** |透過 Azure Active Directory 端點驗證 |
| **VaultGet** |[取得金鑰保存庫的相關資訊](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[建立或更新金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[刪除金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[更新金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[列出資源群組中的所有金鑰保存庫](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[建立金鑰](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[取得金鑰的相關資訊](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[將金鑰匯入保存庫](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[備份金鑰](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[刪除金鑰](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[還原金鑰](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[使用金鑰簽署](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[使用金鑰驗證](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[包裝金鑰](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[解除包裝金鑰](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[使用金鑰加密](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[使用金鑰解密](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[更新金鑰](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[列出保存庫中的金鑰](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[列出金鑰的版本](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[建立密碼](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[取得祕密](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[更新密碼](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[刪除秘密](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[列出保存庫中的密碼](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[列出密碼的版本](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>使用 Azure 監視器記錄

您可以使用 Azure 監視器記錄中的 Key Vault 解決方案來檢閱 Key Vault 的 `AuditEvent` 記錄。 在 Azure 監視器記錄中，您可以使用記錄查詢來分析資料，並取得所需的資訊。 

如需詳細資訊 (包括如何進行此設定)，請參閱 [Azure 監視器中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a><a id="next"></a>後續步驟

如需在 .NET Web 應用程式中使用 Azure Key Vault 的教學課程，請參閱[從 Web 應用程式使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。

如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](developers-guide.md)。

如需 Azure 金鑰保存庫的 Azure PowerShell 1.0 Cmdlet 清單，請參閱 [Azure 金鑰保存庫 Cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)。
