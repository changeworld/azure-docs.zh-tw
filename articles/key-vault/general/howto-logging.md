---
title: 如何啟用 Azure Key Vault 記錄
description: 如何為 Azure Key Vault 啟用記錄，以將資訊儲存在您提供的 Azure 儲存體帳戶中。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 5e0007f3b0dad8a68e9d81cebbe9fe24b5a7db3c
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93285653"
---
# <a name="how-to-enable-key-vault-logging"></a>如何啟用 Key Vault 記錄

在建立一或多個金鑰保存庫之後，您可能會想要監視金鑰保存庫的存取方式、時間和存取者。 如需此功能的完整詳細資訊，請參閱 [Key Vault 記錄](logging.md)。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，必須具備下列項目：

* 所使用的現有金鑰保存庫。  
* Azure CLI 或 Azure PowerShell。
* 足夠的 Azure 儲存體以儲存金鑰保存庫記錄。

如果您選擇在本機安裝和使用 CLI，您將需要 Azure CLI 2.0.4 版版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。 若要使用 CLI 登入 Azure，您可以輸入：

```azurecli-interactive
az login
```

如果您選擇在本機安裝和使用 PowerShell，您將需要 Azure PowerShell 模組1.0.0 版或更新版本。 執行 `$PSVersionTable.PSVersion` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，則也需要執行 `Connect-AzAccount` 以建立與 Azure 的連線。

```powershell-interactive
Connect-AzAccount
```

## <a name="connect-to-your-key-vault-subscription"></a>連接到您的 Key Vault 訂用帳戶

設定金鑰記錄的第一個步驟是連接到包含金鑰保存庫的訂用帳戶。 如果您有多個與帳戶相關聯的訂用帳戶，這點特別重要。

使用 Azure CLI 時，您可以使用 [az account list](/cli/azure/account?view=azure-cli-latest#az_account_list) 命令來查看您的所有訂用帳戶，然後使用 [az 帳戶集](/cli/azure/account?view=azure-cli-latest#az_account_set)連接到其中一個：

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

使用 Azure PowerShell 時，您可以先使用 [>select-azsubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) 指令程式來列出您的訂用帳戶，然後使用 set-azcoNtext 指令 [程式](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) 連接到訂用帳戶： 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>為您的記錄建立儲存體帳戶

雖然您可以為記錄使用現有的儲存體帳戶，但我們將建立專用於 Key Vault 記錄的新儲存體帳戶。 

為了進一步簡化管理，我們也會使用包含金鑰保存庫的同一個資源群組。 在 [Azure CLI 快速入門](quick-create-cli.md) 和 [Azure PowerShell 快速入門](quick-create-powershell.md)中，此資源群組會命名為 **myResourceGroup** ，且位置為 *eastus* 。 視情況將這些值取代為您自己的值。 

我們也必須提供儲存體帳戶名稱。 儲存體帳戶名稱必須是唯一的，長度介於3到24個字元之間，而且只能使用數位和小寫字母。  最後，我們會建立「Standard_LRS」 SKU 的儲存體帳戶。

使用 Azure CLI，請使用 [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) 命令。

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

使用 Azure PowerShell，請使用 [new-azstorageaccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) Cmdlet。 您將需要提供對應至資源群組的位置。

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

在任一種情況下，請注意儲存體帳戶的「識別碼」。 Azure CLI 作業會傳回輸出中的 "id"。 若要取得具有 Azure PowerShell 的「識別碼」，請使用 [new-azstorageaccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) 並將輸出指派給變數 $sa。 然後，您可以使用 $sa 的識別碼來查看儲存體帳戶。 (「$sa。CoNtext "屬性也將在本文稍後使用。 ) 

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

儲存體帳戶的「識別碼」格式將會是「/subscriptions/<您的訂用帳戶識別碼>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<您的唯一-儲存體帳戶名稱>」。

> [!NOTE]
> 如果您決定使用現有的儲存體帳戶，它必須使用與金鑰保存庫相同的訂用帳戶，而且必須使用 Azure Resource Manager 部署模型，而不是傳統部署模型。

## <a name="obtain-your-key-vault-resource-id"></a>取得您的金鑰保存庫資源識別碼

在 [CLI 快速入門](quick-create-cli.md) 和 [PowerShell 快速入門](quick-create-powershell.md)中，您已建立具有唯一名稱的金鑰。  請在下列步驟中再次使用該名稱。  如果您不記得金鑰保存庫的名稱，可以使用 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) 命令或 Azure PowerShell [>new-azkeyvault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) Cmdlet 來列出它們。

使用您的金鑰保存庫名稱來尋找其資源識別碼。  使用 Azure CLI，請使用 [az keyvault show](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show) 命令。

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

使用 Azure PowerShell 時，請使用 [>new-azkeyvault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) Cmdlet。

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

您金鑰保存庫的資源識別碼將採用 "/subscriptions/<您的訂用帳戶識別碼>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<您的唯一 KeyVault 名稱>" 格式。 請記下它以進行下一個步驟。

## <a name="enable-logging-using-azure-powershell"></a>使用 Azure PowerShell 啟用記錄

為了啟用 Key Vault 的記錄功能，我們將使用 Azure CLI [az monitor 診斷設定建立](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) 命令或 [>set-azdiagnosticsetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) 指令程式，以及儲存體帳戶識別碼和金鑰保存庫資源識別碼。

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

使用 Azure PowerShell，我們將使用 [>set-azdiagnosticsetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) 指令程式，並將 **啟用** 的旗標設為 **$true** ，並將類別設定為 `AuditEvent` (Key Vault 記錄) 的唯一類別：

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

您可以選擇性地設定記錄的保留原則，以便在指定的一段時間之後自動刪除較舊的記錄。 例如，您可以設定設定保留原則，以自動刪除超過90天的記錄。

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

使用 Azure PowerShell 時，請使用 [>set-azdiagnosticsetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) Cmdlet。 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

記錄的內容：

* 所有已驗證的 REST API 要求，包括因為存取權限、系統錯誤或要求錯誤而發生的失敗要求。
* 對金鑰保存庫本身所執行的作業，包括建立、刪除、設定金鑰保存庫存取原則，以及更新金鑰保存庫屬性 (例如標記)。
* 金鑰保存庫中的金鑰和祕密作業，包括：
  * 建立、修改或刪除這些金鑰或祕密。
  * 簽署、驗證、加密、解密、包裝和解除包裝金鑰、取得秘密，以及列出金鑰和祕密 (及其版本)。
* 產生 401 回應的未經驗證要求。 例如，沒有持有人權杖的要求、格式不正確或已過期的要求，或具有無效權杖的要求。  
* 即將過期、已過期和保存庫存取原則已變更的事件方格通知事件 (新版本事件未記錄)。 不論金鑰保存庫上是否已建立事件訂用帳戶，都會記錄事件。 如需詳細資訊，請參閱 [Key Vault 的事件方格事件結構描述](../../event-grid/event-schema-key-vault.md)

## <a name="access-your-logs"></a>存取記錄

Key Vault 記錄會儲存在您提供之儲存體帳戶的 "auditevent" 容器中。 若要查看記錄，您必須下載 Blob。

首先，列出容器中的所有 blob。  使用 Azure CLI，請使用 [az storage blob list](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list) 命令。

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

使用 Azure PowerShell，請使用 [AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) 列出此容器中的所有 blob，然後輸入：

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

您會從 Azure CLI 命令或 Azure PowerShell Cmdlet 的輸出中看到，blob 的名稱格式為 `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` 。 日期和時間值使用 UTC。

因為您可以使用相同的儲存體帳戶收集多個資源的記錄，所以 Blob 名稱中的完整資源識別碼適合用來只存取或下載所需 Blob。 但在這麼做之前，我們要先討論如何下載所有 blob。

使用 Azure CLI，使用 [az storage blob 下載](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) 命令，將 blob 的名稱傳遞給它，並將路徑傳遞至您要儲存結果的檔案名。

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

使用 Azure PowerShell，使用 [AzStorageBlobs 指令程式](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) 取得 blob 的清單，然後使用管線將其傳送至 [>get-azstorageblobcontent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) Cmdlet，以將記錄下載至您選擇的路徑。

```powershell-interactive
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

當您在 PowerShell 中執行第二個 Cmdlet 時， **/** blob 名稱中的分隔符號會在目的地資料夾下建立完整資料夾結構。 您將使用此結構來下載 Blob 並儲存為檔案。

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

如需如何讀取記錄的詳細資訊，請參閱 [Key Vault 記錄：解讀您的 Key Vault 記錄](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>使用 Azure 監視器記錄

您可以使用 Azure 監視器記錄中的 Key Vault 解決方案來檢閱 Key Vault 的 `AuditEvent` 記錄。 在 Azure 監視器記錄中，您可以使用記錄查詢來分析資料，並取得所需的資訊。

如需詳細資訊 (包括如何進行此設定)，請參閱 [Azure 監視器中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a>後續步驟

- 如需概念性資訊（包括如何解讀 Key Vault 記錄），請參閱 [Key Vault 記錄](logging.md)
- 如需在 .NET Web 應用程式中使用 Azure Key Vault 的教學課程，請參閱[從 Web 應用程式使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。
- 如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](developers-guide.md)。