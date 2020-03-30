---
title: 在範本中使用部署腳本 |微軟文檔
description: 在 Azure 資源管理器範本中使用部署腳本。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: jgao
ms.openlocfilehash: 7ff91545b1b7ab1920f437e0c3a5410270efaac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153245"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>在範本中使用部署腳本（預覽）

瞭解如何在 Azure 資源範本中使用部署腳本。 使用稱為`Microsoft.Resources/deploymentScripts`的新資源類型，使用者可以在範本部署中執行部署腳本並查看執行結果。 這些腳本可用於執行自訂步驟，例如：

- 將使用者添加到目錄
- 創建應用註冊
- 執行資料平面操作，例如，複製 blob 或種子資料庫
- 查找並驗證許可證金鑰
- 建立自我簽署憑證
- 在 Azure AD 中創建物件
- 從自訂系統查找 IP 位址塊

部署腳本的好處：

- 易於編碼、使用和調試。 您可以在喜愛的開發環境中開發部署腳本。 腳本可以嵌入到範本或外部指令檔中。
- 您可以指定指令碼語言和平臺。 目前，支援 Linux 環境中的 Azure PowerShell 和 Azure CLI 部署腳本。
- 允許指定用於執行腳本的標識。 目前，僅支援[Azure 使用者分配的託管標識](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。
- 允許將命令列參數傳遞給腳本。
- 可以指定腳本輸出並將其傳回部署。

> [!IMPORTANT]
> 系統會在相同的資源群組中建立兩個部署指令碼資源 (儲存體帳戶和容器執行個體)，用以執行指令碼和疑難排解。 當部署腳本執行處於終端狀態時，腳本服務通常會刪除這些資源。 在資源刪除之前，您需支付資源費用。 要瞭解更多資訊，請參閱[清理部署腳本資源](#clean-up-deployment-script-resources)。

## <a name="prerequisites"></a>Prerequisites

- **具有參與者角色的使用者分配的託管標識到目標資源組**。 此身分識別會用來執行部署指令碼。 要在資源組之外執行操作，您需要授予其他許可權。 例如，如果要創建新的資源組，請將標識分配給訂閱級別。

  > [!NOTE]
  > 部署腳本引擎在後臺創建存儲帳戶和容器實例。  如果訂閱尚未註冊 Azure 存儲帳戶 （Microsoft.Storage） 和 Azure 容器實例 （Microsoft.ContainerInstance） 資源，則需要在訂閱級別具有參與者角色的使用者分配的託管標識供應商。

  要創建標識，請參閱通過使用 Azure 門戶 、[或使用 Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)或使用[Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)[創建使用者分配的託管標識](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 您在部署範本時將需要身分識別的識別碼。 此身分識別的格式為：

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  使用以下 CLI 或 PowerShell 腳本通過提供資源組名稱和標識名稱來獲取 ID。

  # <a name="cli"></a>[Cli](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[電源外殼](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure 電源外殼**或**Azure CLI**。 有關受支援的 Azure PowerShell 版本的清單，請參閱[此處](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)。有關受支援的 Azure CLI 版本的清單，請參閱[此處](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)。

    >[!IMPORTANT]
    > 部署腳本使用來自微軟容器註冊表 （MCR） 的可用 CLI 映射。 驗證部署腳本的 CLI 映射大約需要一個月的時間。 不要使用 30 天內發佈的 CLI 版本。 要查找映射的發佈日期，請參閱 Azure [CLI 版本資訊](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest)。 如果使用不支援的版本，錯誤訊息將列出受支援的版本。

    部署範本不需要這些版本。 但是，在本地測試部署腳本需要這些版本。 請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 您可以使用預配置的 Docker 映射。  請參閱[配置開發環境](#configure-development-environment)。

## <a name="sample-templates"></a>範例範本

下面的 json 就是一個例子。  最新的範本架構可以[在這裡](/azure/templates/microsoft.resources/deploymentscripts)找到。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> 該示例用於演示目的。  **腳本內容和****主腳本 Uri 不能**共存于範本中。

屬性值詳細資訊：

- **標識**：部署腳本服務使用使用者分配的託管標識執行腳本。 目前，僅支援使用者分配的託管標識。
- **類型**：指定腳本的類型。 目前，Azure PowerShell 和 Azure CLI 腳本是支援的。 這些值是**AzurePowerShell**和**AzureCLI**。
- **forceUpdateTag**： 在範本部署之間更改此值會強制部署腳本重新執行。 使用需要設置為參數的預設值的 newGuid（） 或 utcNow（） 函數。 若要深入了解，請參閱[執行指令碼多次](#run-script-more-than-once)。
- **azPowerShellVersion**/**azCliVersion：** 指定要使用的模組版本。 有關受支援的 PowerShell 和 CLI 版本的清單，請參閱[先決條件](#prerequisites)。
- **參數**：指定參數值。 多個值應以空格分隔。
- **腳本內容**：指定腳本內容。 要運行外部腳本，請使用`primaryScriptUri`。 有關示例，請參閱[使用內聯腳本](#use-inline-scripts)[和使用外部腳本](#use-external-scripts)。
- **主腳本庫**：指定具有受支援檔副檔名的主部署腳本的可公開訪問的 Url。
- **支援ScriptUris：** 指定一組可公開訪問的 Url 來支援在`ScriptContent`或`PrimaryScriptUri`中調用的檔。
- **超時**：指定[ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)中指定的最大允許腳本執行時間。 預設值為 **P1D**。
- **清理首選項**。 指定在腳本執行進入終端狀態時清理部署資源的首選項。 預設設置為 **"始終**"，這意味著刪除資源，儘管終端狀態（成功，失敗，已取消）。 若要深入了解，請參閱[清除部署指令碼資源](#clean-up-deployment-script-resources)。
- **保留間隔**：指定在部署腳本執行達到終端狀態後服務保留部署腳本資源的時間間隔。 在此持續時間到期時，將刪除部署腳本資源。 持續時間基於[ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)。 預設值為**P1D，** 表示 7 天。 當 cleanupPreference 設定為 *OnExpiration* 時，就會使用此屬性。 當前未啟用 *"過期*"屬性。 若要深入了解，請參閱[清除部署指令碼資源](#clean-up-deployment-script-resources)。

### <a name="additional-samples"></a>其他範例

- [創建證書並將其分配給金鑰保存庫](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [創建使用者分配的託管標識並將其分配給資源組，並運行部署腳本](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)。

> [!NOTE]
> 建議創建使用者分配的標識並提前授予許可權。 如果在運行部署腳本的同一範本中創建標識並授予許可權，則可能會收到登錄和許可權相關的錯誤。 許可權生效需要一些時間。

## <a name="use-inline-scripts"></a>使用內聯腳本

以下範本具有一個使用`Microsoft.Resources/deploymentScripts`類型定義的資源。 突出顯示的部分是內聯腳本。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> 因為內嵌部署指令碼會以雙引號括住，所以部署指令碼內的字串必須以單引號括住。 PowerShell 的逸出字元是 **&#92;**。 您還可以考慮使用字串替換，如上一個 JSON 示例中所示。 請參閱名稱參數的預設值。

該腳本採用一個參數，並輸出參數值。 **部署腳本輸出**用於存儲輸出。  在輸出部分中，**值**行演示如何訪問存儲的值。 `Write-Output`用於調試目的。 要瞭解如何訪問輸出檔案，請參閱[調試部署腳本](#debug-deployment-scripts)。  有關屬性說明，請參閱[示例範本](#sample-templates)。

要運行腳本，請選擇 **"嘗試它**以打開雲外殼"，然後將以下代碼粘貼到 shell 窗格中。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

輸出看起來如下：

![資源管理器範本部署腳本 hello 世界輸出](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>使用外部腳本

除了內聯腳本之外，您還可以使用外部指令檔。 僅支援具有**ps1**檔副檔名的主 PowerShell 腳本。 對於 CLI 腳本，主腳本可以具有任何擴展（或沒有擴展），只要腳本是有效的 bash 腳本。 要使用外部指令檔，請`scriptContent`替換為`primaryScriptUri`。 例如：

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

要查看示例，請[在此處](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)選擇 。

外部指令檔必須可訪問。  要保護存儲在 Azure 存儲帳戶中的指令檔，請參閱[教程：Azure 資源管理器範本部署中的安全專案](./template-tutorial-secure-artifacts.md)。

## <a name="use-supporting-scripts"></a>使用支援腳本

您可以將複雜邏輯分離到一個或多個支援指令檔中。 如果需要`supportingScriptURI`，該屬性允許您向支援指令檔提供一組 URI：

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

可以從內聯腳本和主指令檔調用支援指令檔。 支援指令檔對檔副檔名沒有限制。

支援檔在運行時複製到腳本/腳本輸入。 使用相對路徑引用來自內聯腳本和主指令檔中的支援檔。

## <a name="work-with-outputs-from-powershell-script"></a>使用 PowerShell 腳本的輸出

以下範本演示如何在兩個部署腳本資源之間傳遞值：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

在第一個資源中，定義一個稱為 **$DeploymentScriptOutputs**的變數，並用它來存儲輸出值。 要從範本中的另一個資源訪問輸出值，請使用：

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>使用 CLI 腳本的輸出

與 PowerShell 部署腳本不同，CLI/bash 支援不會公開用於存儲腳本輸出的通用變數，而是存在一個稱為**AZ_SCRIPTS_OUTPUT_PATH**的環境變數，用於存儲腳本輸出檔案所在的位置。 如果部署腳本是從資源管理器範本運行的，則 Bash shell 會自動為您設置此環境變數。

部署腳本輸出必須保存在AZ_SCRIPTS_OUTPUT_PATH位置，並且輸出必須是有效的 JSON 字串物件。 檔的內容必須保存為鍵值對。 例如，字串陣列存儲為 [我的結果]： [ "foo"，"bar"* 。  僅存儲陣列結果（例如"foo"，"bar"）無效。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/)在上一個示例中使用。 它附帶容器圖像。 請參閱[配置開發環境](#configure-development-environment)。

## <a name="develop-deployment-scripts"></a>開發部署腳本

### <a name="handle-non-terminating-errors"></a>處理非終止錯誤

通過使用部署腳本中的[**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
)變數，您可以控制 PowerShell 如何回應非終止錯誤。 部署腳本引擎不設置/更改值。  儘管為$ErrorActionPreference設置了值，但當腳本遇到錯誤時，部署腳本會將資源預配狀態設置為 *"失敗*"。

### <a name="pass-secured-strings-to-deployment-script"></a>將安全字串傳遞給部署腳本

在您的容器執行個體中設定環境變數，可讓您提供由容器執行之應用程式或指令碼的動態設定。 部署腳本處理不安全和安全的環境變數的方式與 Azure 容器實例相同。 有關詳細資訊，請參閱[容器實例中的"設置環境變數](../../container-instances/container-instances-environment-variables.md#secure-values)"。

## <a name="debug-deployment-scripts"></a>調試部署腳本

腳本服務創建[存儲帳戶](../../storage/common/storage-account-overview.md)和[容器實例](../../container-instances/container-instances-overview.md)以執行腳本。 這兩個資源在資源名稱中都有**腳本**尾碼。

![資源管理器範本部署腳本資源名稱](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

使用者腳本、執行結果和斯特寫檔存儲在存儲帳戶的檔共用中。 有一個資料夾叫做 **"腳本**"。 在資料夾中，還有兩個用於輸入和輸出檔案的資料夾 **：azscriptinput**和**azscript 輸出**。

輸出檔案夾包含 **executionresult.json** 和指令碼輸出檔案。 您可以在**執行結果**中看到腳本執行錯誤訊息。 僅當腳本成功執行時，才會創建輸出檔案。 輸入資料夾包含系統 PowerShell 指令檔和使用者部署指令檔。 您可以將使用者部署指令檔替換為修訂後的指令檔，並從 Azure 容器實例重新運行部署腳本。

通過使用 REST API，可以在資源組級別和訂閱級別獲取部署腳本資源部署資訊：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

下面的示例使用[ARMClient](https://github.com/projectkudu/ARMClient)：

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

輸出如下：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

輸出顯示部署狀態和部署腳本資源指示。

以下 REST API 返回日誌：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

它僅在刪除部署腳本資源之前工作。

要查看門戶中的部署腳本資源，請選擇 **"顯示隱藏類型**：

![資源管理器範本部署腳本，顯示隱藏類型、門戶](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>清理部署腳本資源

部署腳本創建存儲帳戶和容器實例，用於執行部署腳本和存儲調試資訊。 這兩個資源與預配資源在同一資源組中創建，腳本服務將在腳本過期時刪除這些資源。 您可以控制這些資源的生命週期。  在刪除它們之前，您都會為這兩個資源收費。 有關價格資訊，請參閱[容器實例定價](https://azure.microsoft.com/pricing/details/container-instances/)和[Azure 存儲定價](https://azure.microsoft.com/pricing/details/storage/)。

這些資源的生命週期由範本中的以下屬性控制：

- **清理首選項**：當腳本執行處於終端狀態時，請清理首選項。  支援的值為：

  - **始終**：一旦腳本執行進入終端狀態，請刪除資源。 由於清除資源後部署腳本資源可能仍然存在，因此系統腳本會在刪除資源之前將腳本執行結果（例如，停滯、輸出、傳回值等）複製到資料庫。
  - **成功**：僅在腳本執行成功時刪除資源。 您仍然可以訪問資源以查找調試資訊。
  - **過期**時：僅在**保留間隔**設置過期時刪除資源。 此屬性當前已禁用。

- **保留間隔**：指定將保留腳本資源的時間間隔，之後將過期和刪除。

> [!NOTE]
> 不建議將部署腳本資源用於其他目的。

## <a name="run-script-more-than-once"></a>多次運行腳本

部署腳本執行是一種冪等操作。 如果未更改任何部署腳本資源屬性（包括內聯腳本），則在重新部署範本時不會執行該腳本。 部署腳本服務將範本中的資源名稱與同一資源組中的現有資源進行比較。 如果要多次執行同一部署腳本，有兩個選項：

- 更改部署腳本資源的名稱。 例如，使用[utcNow](./template-functions-string.md#utcnow)範本函數作為資源名稱或資源名稱的一部分。 更改資源名稱將創建新的部署腳本資源。 它有利於保留腳本執行歷史記錄。

    > [!NOTE]
    > utcNow 函數只能在參數的預設值中使用。

- 在範本屬性中`forceUpdateTag`指定不同的值。  例如，使用 utcNow 作為值。

> [!NOTE]
> 編寫冪等化的部署腳本。 這可確保如果它們再次意外運行，不會導致系統更改。 例如，如果部署腳本用於創建 Azure 資源，則在創建該資源之前驗證該資源不存在，因此該腳本將成功或您不再創建資源。

## <a name="configure-development-environment"></a>設定開發環境

您可以使用預配置的 Docker 容器映射作為部署腳本開發環境。 以下過程演示如何在 Windows 上配置 Docker 映射。 對於 Linux 和 Mac，您可以在 Internet 上找到資訊。

1. 在開發電腦上安裝[Docker 桌面](https://www.docker.com/products/docker-desktop)。
1. 打開 Docker 桌面。
1. 從工作列中選擇 Docker 桌面圖示，然後選擇 **"設置**"。
1. 選擇 **"共用磁碟機**"，選擇要對容器可用的本地磁碟機，然後選擇 **"應用"**

    ![資源管理器範本部署腳本 Docker 磁碟機](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. 在提示符處輸入視窗憑據。
1. 打開終端視窗，命令提示符或 Windows PowerShell（請勿使用 PowerShell ISE）。
1. 將部署腳本容器映射拉到本地電腦：

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    該示例使用版本 PowerShell 2.7.0。

    要從 Microsoft 容器註冊表 （MCR） 中提取 CLI 映射：

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    此示例使用版本 CLI 2.0.80。 部署腳本使用[此處](https://hub.docker.com/_/microsoft-azure-cli)找到的預設 CLI 容器映射。

1. 在本地運行 Docker 映射。

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    將**&lt;主機驅動程式字母>** 和**&lt;主機目錄名稱>** 與共享磁碟機上的現有資料夾替換。  它將資料夾映射到容器中的 **/data**資料夾。 有關示例，要映射 D：\docker：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-這意味著**保持容器映射處於活動狀態。

    CLI 示例：

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. 選擇在收到提示時**共用它**。
1. 以下螢幕截圖顯示了如何運行 PowerShell 腳本，因為 d：\docker 資料夾中有一個 helloworld.ps1 檔。

    ![資源管理器範本部署腳本 docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

測試腳本成功後，您可以將其用作部署腳本。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何使用部署腳本。 要流覽部署腳本教程，請進行以下說明：

> [!div class="nextstepaction"]
> [教程：在 Azure 資源管理器範本中使用部署腳本](./template-tutorial-deployment-script.md)