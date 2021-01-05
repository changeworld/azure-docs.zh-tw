---
title: 在範本中使用部署指令碼 | Microsoft Docs
description: 使用 Azure Resource Manager 範本中的部署指令碼。
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/28/2020
ms.author: jgao
ms.openlocfilehash: 4d2a55355318a1bf916017fa77026a87a95b7f57
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809712"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>在 ARM 範本中使用部署腳本

瞭解如何在 Azure 資源範本中使用部署腳本 (ARM 範本) 。 在呼叫新的資源類型時 `Microsoft.Resources/deploymentScripts` ，使用者可以在範本部署中執行腳本，並查看執行結果。 這些指令碼可以用來執行自訂步驟，例如：

- 將使用者新增到目錄
- 執行資料平面作業，例如複製 Blob 或種子資料庫
- 查詢及驗證授權金鑰
- 建立自我簽署憑證
- 在 Azure AD 中建立物件
- 從自訂系統查詢 IP 位址區塊

部署指令碼的優點：

- 易於編寫程式碼、使用和偵錯。 您可以在慣用的開發環境中開發部署指令碼。 這些指令碼可以內嵌在範本或外部指令檔中。
- 您可以指定指令碼語言和平台。 目前支援在 Linux 環境上的 Azure PowerShell 和 Azure CLI 部署指令碼。
- 允許將命令列引數傳遞至指令碼。
- 可以指定指令碼輸出，並將其傳回至部署。

部署指令碼資源僅適用於可使用 Azure 容器執行個體的區域。  請參閱 [Azure 容器執行個體在 Azure 區域中的資源可用性](../../container-instances/container-instances-region-availability.md)。

> [!IMPORTANT]
> 執行執行個體及疑難排解時，需要儲存體帳戶和容器執行個體。 您可以選擇指定現有的儲存體帳戶，否則指令碼服務會自動建立儲存體帳戶和容器執行個體。 當部署指令碼執行進入終端狀態時，指令碼服務通常會刪除這兩個自動建立的資源。 在資源刪除之前，您需支付資源費用。 若要深入了解，請參閱[清除部署指令碼資源](#clean-up-deployment-script-resources)。

> [!IMPORTANT]
> DeploymentScripts 資源 API 版本2020-10-01 支援 [OnBehalfofTokens (OBO) ](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md)。 藉由使用 OBO，部署腳本服務會使用部署主體的權杖來建立基礎資源，以執行部署腳本，其中包括 Azure 容器實例、Azure 儲存體帳戶，以及受控識別的角色指派。 在較舊的 API 版本中，會使用受控識別來建立這些資源。
> Azure 登入的重試邏輯現在已內建至包裝函式腳本。 如果您在執行部署腳本的相同範本中授與許可權。 部署腳本服務會以10秒的間隔重試登入10分鐘，直到受控識別角色指派已複寫為止。

## <a name="configure-the-minimum-permissions"></a>設定最小許可權

針對部署腳本 API 2020-10-01 版或更新版本，部署主體可用來建立要執行部署腳本資源所需的基礎資源，也就是儲存體帳戶和 Azure 容器實例。 如果腳本需要向 Azure 進行驗證，並執行 Azure 特定的動作，建議您為腳本提供使用者指派的受控識別。 受控識別必須具有必要的存取權，才能完成腳本中的作業。

若要設定最低許可權許可權，您需要：

- 使用下列屬性將自訂角色指派給部署主體：

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  如果 Azure 儲存體和 Azure 容器實例資源提供者尚未註冊，您也需要新增 `Microsoft.Storage/register/action` 和 `Microsoft.ContainerInstance/register/action` 。

- 如果使用受控識別，則部署主體需要 **受控識別操作員** 角色 (內建角色) 指派給受控識別資源。

## <a name="sample-templates"></a>範例範本

下列 JSON 是範例。 如需詳細資訊，請參閱最新的 [範本架構](/azure/templates/microsoft.resources/deploymentscripts)。

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
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
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
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
> 此範例是為了示範之用。 屬性 `scriptContent` 和 `primaryScriptUri` 不能並存于範本中。

屬性值詳細資料：

- `identity`：針對部署腳本 API 2020-10-01 版或更新版本，除非您需要在腳本中執行任何 Azure 特定的動作，否則使用者指派的受控識別是選擇性的。  針對 API 版本 2019-10-01-preview，需要受控識別，因為部署腳本服務會使用它來執行腳本。 目前僅支援使用者指派的受控識別。
- `kind`：指定指令碼的類型。 目前支援 Azure PowerShell 和 Azure CLI 腳本。 值為 **AzurePowerShell** 和 **AzureCLI**。
- `forceUpdateTag`：變更範本部署之間的這個值會強制部署腳本重新執行。 如果您使用 `newGuid()` 或 `utcNow()` 函數，則這兩個函式只能用於參數的預設值。 若要深入了解，請參閱[執行指令碼多次](#run-script-more-than-once)。
- `containerSettings`：指定自訂 Azure 容器實例的設定。  `containerGroupName` 這是用來指定容器組名。 如果未指定，則會自動產生組名。
- `storageAccountSettings`：指定要使用現有儲存體帳戶的設定。 如果未指定，則會自動建立儲存體帳戶。 請參閱[使用現有儲存體帳戶](#use-existing-storage-account)。
- `azPowerShellVersion`/`azCliVersion`：指定要使用的模組版本。 請參閱支援的 [Azure PowerShell 版本](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)清單。 請參閱支援的 [Azure CLI 版本](https://mcr.microsoft.com/v2/azure-cli/tags/list)清單。

  >[!IMPORTANT]
  > 部署腳本會使用來自 Microsoft Container Registry 的可用 CLI 映射 (MCR) 。 需要約一個月的時間來認證適用於部署指令碼的 CLI 映像。 請勿使用在 30 天內發行的 CLI 版本。 若要尋找映像的發行日期，請參閱 [Azure CLI 版本資訊](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true)。 如果使用不支援的版本，則錯誤訊息會列出支援的版本。

- `arguments`：指定參數值。 多個值應以空格分隔。

  部署腳本會叫用 [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) 系統呼叫，以將引數分割為字串陣列。 這個步驟是必要的，因為引數會以 [命令屬性](/rest/api/container-instances/containergroups/createorupdate#containerexec) 的形式傳遞至 Azure 容器實例，而 command 屬性是字串的陣列。

  如果引數包含已轉義的字元，請使用 [JsonEscaper](https://www.jsonescaper.com/) 來將字元換成雙引號。 將原始的逸出字元串貼入工具中，然後選取 [ **Escape**]。  此工具會輸出雙精度浮點數的字串。 例如，在先前的範例範本中，引數為 `-name \"John Dole\"` 。 已轉義的字串為 `-name \\\"John Dole\\\"` 。

  若要將類型 object 的 ARM 範本參數傳遞為引數，請使用 [字串 ( # B1 ](./template-functions-string.md#string) 函數將物件轉換為字串，然後使用 [Replace ( # B3 ](./template-functions-string.md#replace) 函式將任何取代為 `\"` `\\\"` 。 例如：

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  如需詳細資訊，請參閱 [範例範本](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json)。

- `environmentVariables`：指定要傳遞至腳本的環境變數。 如需相關資訊，請參閱[開發部署指令碼](#develop-deployment-scripts)。
- `scriptContent`：指定指令碼內容。 若要執行外部指令碼，請改用 `primaryScriptUri`。 如需範例，請參閱[使用內嵌指令碼](#use-inline-scripts)和[使用外部指令碼](#use-external-scripts)。
  > [!NOTE]
  > Azure 入口網站無法剖析具有多行的部署腳本。 若要使用來自 Azure 入口網站的部署腳本來部署範本，您可以使用分號將 PowerShell 命令連結到一行，或使用 `primaryScriptUri` 具有外部腳本檔案的屬性。

- `primaryScriptUri`：以支援的副檔名指定主要部署腳本的可公開存取 Url。
- `supportingScriptUris`：指定可公開存取的 Url 陣列，以支援在或中呼叫的 `scriptContent` 檔案 `primaryScriptUri` 。
- `timeout`：指定以 [ISO 8601 格式](https://en.wikipedia.org/wiki/ISO_8601)指定的指令碼執行時間允許上限。 預設值為 **P1D**。
- `cleanupPreference`. 指定當指令碼執行進入終端狀態時，清除部署資源的喜好設定。 預設設定為 [一律]，這表示不論終端狀態 (成功、失敗、已取消) 皆刪除資源。 若要深入了解，請參閱[清除部署指令碼資源](#clean-up-deployment-script-resources)。
- `retentionInterval`：指定在部署腳本執行到達終止狀態之後，服務保留部署腳本資源的間隔。 此持續時間到期後，即會刪除部署指令碼資源。 持續時間以 [ISO 8601 模式](https://en.wikipedia.org/wiki/ISO_8601)為基礎。 保留間隔介於1到26小時 (PT26H) 。 當 `cleanupPreference` 設定為 **OnExpiration** 時，就會使用此屬性。 目前未啟用 **>onexpiration** 屬性。 若要深入了解，請參閱[清除部署指令碼資源](#clean-up-deployment-script-resources)。

### <a name="additional-samples"></a>其他範例

- [範例 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)：建立金鑰保存庫，並使用部署腳本將憑證指派給金鑰保存庫。
- [範例 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json)：在訂用帳戶層級建立資源群組、在資源群組中建立金鑰保存庫，然後使用部署腳本將憑證指派給金鑰保存庫。
- [範例 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json)：建立使用者指派的受控識別、將參與者角色指派給資源群組層級的身分識別、建立金鑰保存庫，然後使用部署腳本將憑證指派給金鑰保存庫。

## <a name="use-inline-scripts"></a>使用內嵌指令碼

下列範本有一個以 `Microsoft.Resources/deploymentScripts` 類型定義的資源。 反白顯示的部分是內嵌指令碼。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> 因為內嵌部署腳本是以雙引號括住，所以部署腳本內的字串必須使用反斜線 (**&#92;**) 或以單引號括住。 您也可以考慮使用字串替代，如先前的 JSON 範例中所示。

指令碼接受一個參數，並輸出參數值。 `DeploymentScriptOutputs` 用於儲存輸出。 在 [輸出] 區段中，這 `value` 一行會顯示如何存取儲存的值。 `Write-Output` 是用來偵錯。 若要瞭解如何存取輸出檔，請參閱 [監視和疑難排解部署腳本](#monitor-and-troubleshoot-deployment-scripts)。 如需屬性描述，請參閱[範例範本](#sample-templates)。

若要執行指令碼，請選取 [試試看] 以開啟 Cloud Shell，然後將下列程式碼貼到 Shell 窗格中。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

輸出看起來如下：

![Resource Manager 範本部署指令碼 Hello World 輸出](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>使用外部指令碼

除了內嵌指令碼之外，您也可以使用外部指令檔。 僅支援具有 _ps1_ 副檔名的主要 PowerShell 指令碼。 針對 CLI 指令碼，只要指令碼是有效的 bash 指令碼，主要指令碼就可以有任何延伸模組 (或不含延伸模組)。 若要使用外部指令檔，請將 `scriptContent` 取代為 `primaryScriptUri`。 例如：

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

如需詳細資訊，請參閱 [範例範本](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json)。

外部指令檔必須可供存取。 若要保護儲存在 Azure 儲存體帳戶中的指令檔，請參閱[部署具有 SAS 權杖的私人 ARM 範本](./secure-template-with-sas-token.md)。

您必須負責確保部署腳本所參考的腳本是或的完整性 `primaryScriptUri` `supportingScriptUris` 。 只參考您信任的指令碼。

## <a name="use-supporting-scripts"></a>使用支援的指令碼

您可以將複雜的邏輯分隔成一個或多個支援的指令檔。 `supportingScriptUris` 屬性可讓您視需要將 URI 陣列提供給支援的指令檔：

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

您可以從內嵌指令碼和主要指令檔呼叫支援的指令檔。 支援的指令檔對副檔名沒有任何限制。

支援的檔案會 `azscripts/azscriptinput` 在執行時間複製到。 使用相對路徑來參考內嵌指令碼和主要指令檔中的支援檔案。

## <a name="work-with-outputs-from-powershell-script"></a>使用 PowerShell 指令碼的輸出

下列範本說明如何在兩個資源之間傳遞值 `deploymentScripts` ：

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

在第一個資源中，您會定義名為的變數 `$DeploymentScriptOutputs` ，並使用它來儲存輸出值。 若要從範本內的另一個資源存取輸出值，請使用：

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>使用 CLI 指令碼的輸出

不同于 PowerShell 部署腳本，CLI/bash 支援不會公開一般變數來儲存腳本輸出，而是會有一個名為的環境變數，用 `AZ_SCRIPTS_OUTPUT_PATH` 來儲存腳本輸出檔所在的位置。 如果部署指令碼是從 Resource Manager 範本執行，Bash shell 會自動為您設定此環境變數。

部署腳本輸出必須儲存在 `AZ_SCRIPTS_OUTPUT_PATH` 位置，而且輸出必須是有效的 JSON 字串物件。 檔案的內容必須儲存為機碼值組。 例如，字串的陣列會儲存為 `{ "MyResult": [ "foo", "bar"] }` 。  例如，只儲存陣列結果 `[ "foo", "bar" ]` 無效。

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

先前的範例會使用 [jq](https://stedolan.github.io/jq/)。 其隨附於容器映像。 請參閱[設定開發環境](#configure-development-environment)。

## <a name="use-existing-storage-account"></a>使用現有的儲存體帳戶

執行執行個體及疑難排解時，需要儲存體帳戶和容器執行個體。 您可以選擇指定現有的儲存體帳戶，否則指令碼服務會自動建立儲存體帳戶和容器執行個體。 使用現有儲存體帳戶的需求：

- 支援的儲存體帳戶類型為：

    | SKU             | 支援的種類     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | 儲存體、StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | 儲存體、StorageV2 |
    | Standard_RAGRS  | 儲存體、StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    這些組合支援檔案共用。 如需詳細資訊，請參閱 [建立 Azure 檔案共用](../../storage/files/storage-how-to-create-file-share.md) 和 [儲存體帳戶類型](../../storage/common/storage-account-overview.md)。

- 尚不支援儲存體帳戶防火牆規則。 如需詳細資訊，請參閱[設定 Azure 儲存體防火牆和虛擬網路](../../storage/common/storage-network-security.md)。
- 部署主體必須具有管理儲存體帳戶的許可權，其中包括讀取、建立、刪除檔案共用。

若要指定現有的儲存體帳戶，請將下列 JSON 新增至的屬性元素 `Microsoft.Resources/deploymentScripts` ：

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`：指定儲存體帳戶的名稱。
- `storageAccountKey`：指定其中一個儲存體帳戶金鑰。 您可以使用 [listKeys ( # B1 ](./template-functions-resource.md#listkeys) 函數來取得金鑰。 例如：

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

如需完整的 `Microsoft.Resources/deploymentScripts` 定義範例，請參閱[範例範本](#sample-templates)。

使用現有的儲存體帳戶時，指令碼服務會建立具有唯一名稱的檔案共用。 如需了解指令碼服務如何清理檔案共用，請參閱[清除部署指令碼資源](#clean-up-deployment-script-resources)。

## <a name="develop-deployment-scripts"></a>開發部署指令碼

### <a name="handle-non-terminating-errors"></a>處理非終止錯誤

您可以使用部署腳本中的變數來控制 PowerShell 如何回應非終止錯誤 `$ErrorActionPreference` 。 如果您的部署腳本中未設定變數，腳本服務會使用預設值 **Continue**。

當腳本在設定時遇到錯誤時， 腳本服務會將資源布建狀態設定為失敗 `$ErrorActionPreference` 。

### <a name="pass-secured-strings-to-deployment-script"></a>將安全的字串傳遞至部署指令碼

在您的容器執行個體中設定環境變數 (EnvironmentVariable)，可讓您提供由容器執行之應用程式或指令碼的動態設定。 部署指令碼會以與 Azure 容器執行個體相同的方式來處理不安全及安全的環境變數。 如需詳細資訊，請參閱[設定容器執行個體中的環境變數](../../container-instances/container-instances-environment-variables.md#secure-values)。

環境變數允許的大小上限為 64 KB。

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>監視部署腳本並進行疑難排解

指令碼服務會建立[儲存體帳戶](../../storage/common/storage-account-overview.md) (除非您指定現有的儲存體帳戶) 和[容器執行個體](../../container-instances/container-instances-overview.md)以執行指令碼。 如果腳本服務自動建立這些資源，則這兩個資源在 `azscripts` 資源名稱中都有尾碼。

![Resource Manager 範本部署指令碼資源名稱](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

使用者指令碼、執行結果和 stdout 檔案會儲存在儲存體帳戶的檔案共用中。 有一個名為的資料夾 `azscripts` 。 在資料夾中，輸入和輸出檔有兩個以上的資料夾： `azscriptinput` 和 `azscriptoutput` 。

輸出檔案夾包含 _executionresult.json_ 和指令碼輸出檔案。 您可以在 _executionresult.json_ 中查看指令碼執行錯誤訊息。 只有在指令碼執行成功時，才會建立輸出檔案。 輸入資料夾包含系統 PowerShell 指令檔和使用者部署指令檔。 您可以使用已修改的檔案來取代使用者部署指令檔，然後從 Azure 容器執行個體重新執行部署指令碼。

### <a name="use-the-azure-portal"></a>使用 Azure 入口網站

部署腳本資源部署完成後，資源會列在 Azure 入口網站中的資源群組底下。 下列螢幕擷取畫面顯示部署腳本資源的 **總覽** 頁面：

![Resource Manager 範本部署腳本入口網站總覽](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

[總覽] 頁面會顯示資源的一些重要資訊，例如「布建 **狀態**」、「 **儲存體帳戶**」、「 **容器實例**」和「 **記錄**」。

您可以從左側功能表中，查看部署腳本內容、傳遞給腳本的引數，以及輸出。 您也可以匯出包含部署腳本的部署腳本範本。

### <a name="use-powershell"></a>使用 PowerShell

您可以使用 Azure PowerShell，在訂用帳戶或資源群組範圍管理部署腳本：

- [AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript)：取得或列出部署腳本。
- [AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog)：取得部署腳本執行的記錄檔。
- [AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript)：移除部署腳本及其相關聯的資源。
- [AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog)：將部署腳本執行的記錄儲存至磁片。

`Get-AzDeploymentScript`輸出類似于：

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>使用 Azure CLI

您可以使用 Azure CLI，在訂用帳戶或資源群組範圍管理部署腳本：

- [az 部署-腳本刪除](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-delete)：刪除部署腳本。
- [az deployment-scripts list](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-list)：列出所有的部署腳本。
- [az deployment-scripts show](/cli/azure/deployment-scripts?view=azure-cli-latest&preserve-view=true#az-deployment-scripts-show)：取出部署腳本。
- [az 部署-腳本顯示-記錄](/cli/azure/deployment-scripts?view=azure-cli-lates&preserve-view=truet#az-deployment-scripts-show-log)：顯示部署腳本記錄檔。

清單命令輸出類似于：

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>使用 Rest API

您可以使用 REST API，在資源群組層級和訂用帳戶層級取得部署指令碼資源部署資訊：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

下列範例會使用 [ARMClient](https://github.com/projectkudu/ARMClient)：

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

輸出如下：

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

下列 REST API 會傳回記錄：

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

只有在刪除部署指令碼資源之前才可運作。

若要在入口網站中查看 deploymentScripts 資源，請選取 [顯示隱藏的類型]：

![Resource Manager 範本部署指令碼、顯示隱藏的類型、入口網站](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>清除部署指令碼資源

執行執行個體及疑難排解時，需要儲存體帳戶和容器執行個體。 您可以選擇指定現有的儲存體帳戶，否則指令碼服務會自動建立儲存體帳戶及容器執行個體。 當部署指令碼執行進入終端狀態時，指令碼服務會刪除這兩個自動建立的資源。 在資源刪除之前，您需支付資源費用。 如需價格資訊，請參閱[容器執行個體定價](https://azure.microsoft.com/pricing/details/container-instances/)和 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

這些資源的生命週期是由範本中的下列屬性所控制：

- `cleanupPreference`：當腳本執行處於終止狀態時，請清除喜好設定。 支援的值為：

  - **一律**：一旦指令碼執行進入終端狀態後，即刪除自動建立的資源。 如果使用現有的儲存體帳戶，指令碼服務會刪除在儲存體帳戶中建立的檔案共用。 因為資源 `deploymentScripts` 可能在清除資源之後仍會存在，所以腳本服務會在刪除資源之前，先保存腳本執行結果，例如 stdout、輸出和傳回值。
  - **OnSuccess**：只有在指令碼執行成功時，才刪除自動建立的資源。 如果使用現有的儲存體帳戶，指令碼服務只會在指令碼執行成功時，才會移除檔案共用。 您仍然可以存取資源來尋找偵錯資訊。
  - **>onexpiration**：只有在設定過期時，才刪除自動建立的資源 `retentionInterval` 。 如果使用現有的儲存體帳戶，腳本服務會移除檔案共用，但會保留儲存體帳戶。

- `retentionInterval`：指定將保留腳本資源的時間間隔，之後將會過期並刪除。

> [!NOTE]
> 不建議針對其他目的使用由指令碼服務產生的儲存體帳戶和容器執行個體。 視指令碼生命週期而定，可能會移除這兩個資源。

容器實例和儲存體帳戶會根據來刪除 `cleanupPreference` 。 但是，如果腳本失敗且 `cleanupPreference` 未設定為 [ **永遠**]，部署程式會自動讓容器執行一小時的時間。 您可以使用此小時來對腳本進行疑難排解。 如果您想要在成功部署之後讓容器保持執行，請將睡眠步驟新增至腳本。 例如，將 [ [開始-睡眠](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep) ] 新增至腳本的結尾。 如果您未新增睡眠步驟，容器會設定為終端狀態，而且即使尚未刪除也無法存取。

## <a name="run-script-more-than-once"></a>執行指令碼超過一次

部署指令碼執行是等冪性作業。 如果未變更任何 `deploymentScripts` 資源屬性 (包括內嵌腳本) ，則當您重新部署範本時，腳本不會執行。 部署指令碼服務會比較範本中的資源名稱與相同資源群組中的現有資源。 如果您想要多次執行相同的部署指令碼，有兩個選項：

- 變更您資源的名稱 `deploymentScripts` 。 例如，使用 [utcNow](./template-functions-date.md#utcnow) 範本函式做為資源名稱，或做為資源名稱的一部分。 變更資源名稱會建立新的 `deploymentScripts` 資源。 它很適合用來保存腳本的執行歷程記錄。

    > [!NOTE]
    > `utcNow`函數只能用於參數的預設值。

- 在 [`forceUpdateTag` 範本] 屬性中指定不同的值。 例如，使用 `utcNow` 做為值。

> [!NOTE]
> 撰寫具有等冪性的部署指令碼。 這可確保如果不小心再次執行，不會造成系統變更。 例如，如果部署指令碼是用來建立 Azure 資源，在建立資源之前請先確認資源不存在，因此指令碼將會成功，或者您不會重新建立資源。

## <a name="configure-development-environment"></a>設定開發環境

您可以使用預先設定的容器映射作為部署腳本開發環境。 如需詳細資訊，請參閱 [在範本中設定部署腳本的開發環境](./deployment-script-template-configure-dev.md)。

成功測試腳本之後，您可以使用它做為範本中的部署腳本。

## <a name="deployment-script-error-codes"></a>部署腳本錯誤碼

| 錯誤碼 | 描述 |
|------------|-------------|
| DeploymentScriptInvalidOperation | 範本中的部署腳本資源定義包含不正確屬性名稱。 |
| DeploymentScriptResourceConflict | 無法刪除處於非終端機狀態的部署腳本資源，且執行未超過1小時。 或者，您也無法使用相同的資源識別碼來重新執行相同的部署腳本 (相同的訂用帳戶、資源組名和資源名稱) 但同時有不同的腳本主體內容。 |
| DeploymentScriptOperationFailed | 部署腳本作業在內部失敗。 請聯絡 Microsoft 支援服務。 |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | 尚未指定現有儲存體帳戶的存取金鑰。|
| DeploymentScriptContainerGroupContainsInvalidContainers | 部署腳本服務所建立的容器群組已從外部修改，並新增了不正確容器。 |
| DeploymentScriptContainerGroupInNonterminalState | 有兩個或多個部署腳本資源在相同的資源群組中使用相同的 Azure 容器實例名稱，但其中一個資源尚未完成執行。 |
| DeploymentScriptStorageAccountInvalidKind | BlobBlobStorage 或 BlobStorage 類型的現有儲存體帳戶不支援檔案共用，因此無法使用。 |
| DeploymentScriptStorageAccountInvalidKindAndSku | 現有的儲存體帳戶不支援檔案共用。 如需支援的儲存體帳戶類型清單，請參閱 [使用現有的儲存體帳戶](#use-existing-storage-account)。 |
| DeploymentScriptStorageAccountNotFound | 儲存體帳戶不存在或已被外部進程或工具刪除。 |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | 指定的儲存體帳戶具有服務端點。 不支援具有服務端點的儲存體帳戶。 |
| DeploymentScriptStorageAccountInvalidAccessKey | 為現有的儲存體帳戶指定的存取金鑰無效。 |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | 儲存體帳戶金鑰格式無效。 請參閱 [管理儲存體帳戶存取金鑰](../../storage/common/storage-account-keys-manage.md)。 |
| DeploymentScriptExceededMaxAllowedTime | 部署腳本的執行時間超過部署腳本資源定義中指定的超時值。 |
| DeploymentScriptInvalidOutputs | 部署腳本輸出不是有效的 JSON 物件。 |
| DeploymentScriptContainerInstancesServiceLoginFailure | 使用者指派的受控識別無法在10次嘗試後以1分鐘的間隔登入。 |
| DeploymentScriptContainerGroupNotFound | 由外部工具或進程刪除部署腳本服務所建立的容器群組。 |
| DeploymentScriptDownloadFailure | 無法下載支援的腳本。 請參閱 [使用支援腳本](#use-supporting-scripts)。|
| DeploymentScriptError | 使用者腳本擲回錯誤。 |
| DeploymentScriptBootstrapScriptExecutionFailed | 啟動程式腳本擲回錯誤。 啟動程式腳本是可協調部署腳本執行的系統腳本。 |
| DeploymentScriptExecutionFailed | 部署腳本執行期間發生未知的錯誤。 |
| DeploymentScriptContainerInstancesServiceUnavailable |  (ACI) 建立 Azure 容器實例時，ACI 擲回服務無法使用的錯誤。 |
| DeploymentScriptContainerGroupInNonterminalState | 建立 (ACI) 的 Azure 容器實例時，另一個部署腳本會在相同範圍內使用相同的 ACI 名稱 (相同的訂用帳戶、資源組名和資源名稱) 。 |
| DeploymentScriptContainerGroupNameInvalid | 指定的 Azure 容器實例名稱 (ACI) 指定的並不符合 ACI 需求。 請參閱針對 [Azure 容器實例中的常見問題進行疑難排解](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment)。|

## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何使用部署指令碼。 逐步解說部署指令碼教學課程：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure Resource Manager 範本中的部署指令碼](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [學習模組：使用部署腳本擴充 ARM 範本](/learn/modules/extend-resource-manager-template-deployment-scripts/)
