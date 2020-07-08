---
title: 使用 Azure Resource Manager 範本建立工作區
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Resource Manager 範本建立新的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: 39c694f4e2afbf5d781a8fde43a7db9c4a255466
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85392656"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

在本文中，您將了解使用 Azure Resource Manager 範本建立 Azure Machine Learning 工作區的數種方式。 Resource Manager 範本可讓您輕鬆地以單一、協調的作業建立資源。 範本是 JSON 文件，其定義部署所需的資源。 它也可以指定部署參數。 參數用來在使用範本時提供輸入值。

如需詳細資訊，請參閱 [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="prerequisites"></a>Prerequisites

* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可[試用免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從 CLI 使用範本，您需要 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>Resource Manager 範本

下列 Resource Manager 範本可用來建立 Azure Machine Learning 工作區和相關聯的 Azure 資源：

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

此範本會建立下列 Azure 服務：

* Azure 資源群組
* Azure 儲存體帳戶
* Azure 金鑰保存庫
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning 工作區

資源群組是保存服務的容器。 各種服務是 Azure Machine Learning 工作區的必要項。

範例範本有兩個參數：

* **位置**是將建立資源群組和服務的位置。

    範本將會針對大部分的資源使用您選取的位置。 例外狀況是 Application Insights 服務，該服務在其他服務可用的所有位置都無法使用。 如果您選取的位置無法使用，服務會建立在美國中南部位置。

* **工作區名稱**是 Azure Machine Learning 工作區的易記名稱。

    > [!NOTE]
    > 工作區名稱不區分大小寫。

    其他服務的名稱會隨機產生。

> [!TIP]
> 雖然與本文件建立關聯的範本會建立新 Azure Container Registry，但您也可建立新的工作區，而不需要建立容器登錄。 當執行需要容器登錄的作業時，將會建立一個。 例如，定型或部署模型。
>
> 您也可以在 Azure Resource Manager 範本中參考現有的容器登錄或儲存體帳戶，而不是建立一個新的。 不過，您使用的容器登錄必須啟用系統__管理員帳戶__。 如需啟用系統管理員帳戶的相關資訊，請參閱系統[管理員帳戶](/azure/container-registry/container-registry-authentication#admin-account)。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

如需範本的詳細資訊，請參閱下列文章：

* [編寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resource 類型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>進階範本

下列範例範本示範如何建立具有三項設定的工作區：

* 啟用工作區的高機密性設定
* 啟用工作區的加密
* 使用現有 Azure Key Vault 來擷取客戶管理的金鑰

如需詳細資訊，請參閱[靜態加密](concept-enterprise-security.md#encryption-at-rest)。

> [!IMPORTANT]
> 使用此範本之前，訂用帳戶必須符合一些特定需求：
> * __Azure Machine Learning__ 應用程式必須是 Azure 訂用帳戶的「參與者」。
> * 您必須具有包含加密金鑰的現有 Azure Key Vault。
> * 您必須在 Azure Key Vault 中擁有存取原則，以授與「取得」、「包裝」和「解除包裝」等 __Azure Cosmos DB__ 應用程式存取權。
> * Azure Key Vault 必須位於打算建立 Azure Machine Learning 工作區的相同區域中。

若要將 Azure Machine Learning 應用程式新增為參與者，請使用下列命令：

1. 若要從 CLI 向 Azure 進行驗證，請使用下列命令：

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. 若要取得 Azure Machine Learning 應用程式的物件識別碼，請使用下列命令。 每個 Azure 訂用帳戶的值可能不同：

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    此命令會傳回物件識別碼，這是一個 GUID。

1. 若要將物件識別碼新增為訂用帳戶的參與者，請使用下列命令。 將 `<object-ID>` 取代為先前步驟中的 GUID。 將 `<subscription-ID>` 取代為 Azure 訂用帳戶的名稱或識別碼：

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

[若要將金鑰新增至 Azure Key Vault](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) ，請使用＜使用 Azure CLI 管理 Key Vault＞一文的＜新增金鑰、祕密或憑證至金鑰保存庫＞一節中資訊。

若要將存取原則新增至金鑰保存庫，請使用「下列命令」：

1. 若要取得 Azure Cosmos DB 應用程式的物件識別碼，請使用下列命令。 每個 Azure 訂用帳戶的值可能不同：

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    此命令會傳回物件識別碼，這是一個 GUID。

1. 若要設定原則，請使用下列命令。 將 `<keyvault-name>` 取代為現有 Azure Key Vault 的名稱。 將 `<object-ID>` 取代為先前步驟中的 GUID：

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

若要取得此範本所需 `cmk_keyvault` (Key Vault 的識別碼) 和 `resource_cmk_uri` (金鑰 URI) 參數的值，請使用下列步驟：

1. 若要取得 Key Vault 識別碼，請使用下列命令：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    此命令會傳回如下值：`/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`。

1. 若要取得客戶管理金鑰的 URI 值，請使用下列命令：

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    此命令會傳回如下值：`https://mykeyvault.vault.azure.net/keys/mykey/{guid}`。

__範例範本__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> 建立工作區之後，即無法變更機密資料、加密、金鑰保存庫識別碼或金鑰識別碼的設定。 若要變更這些值，則必須使用新的值來建立新工作區。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 遵循[從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)的步驟。 當您看見 [編輯範本] 畫面時，貼入此文件的範本。
1. 選取 [儲存] 以使用範本。 提供下列資訊，並同意列出的條款及條件：

   * 訂用帳戶：選取要用於這些資源的 Azure 訂用帳戶。
   * 資源群組：選取或建立資源群組以包含服務。
   * 工作區名稱：要用於將建立之Azure Machine Learning 工作區的名稱。 工作區名稱必須介於 3 到 33 個字元之間。 只能包含英數字元和 '-'。
   * 位置：選取將建立資源的位置。

如需詳細資訊，請參閱[從自訂範本部署資源](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

這個範例假設您已將範本儲存到目前目錄中名為 `azuredeploy.json` 的檔案：

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md)和[使用 SAS 權杖和 Azure PowerShell 部署私用 Resource Manager 範本](../azure-resource-manager/templates/secure-template-with-sas-token.md)。

## <a name="use-the-azure-cli"></a>使用 Azure CLI

這個範例假設您已將範本儲存到目前目錄中名為 `azuredeploy.json` 的檔案：

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

如需詳細資訊，請參閱[使用 Resource Manager 範本與 Azure CLI 來部署資源](../azure-resource-manager/templates/deploy-cli.md)和[使用 SAS 權杖和 Azure CLI 部署私用 Resource Manager 範本](../azure-resource-manager/templates/secure-template-with-sas-token.md)。

## <a name="troubleshooting"></a>疑難排解

### <a name="resource-provider-errors"></a>資源提供者錯誤

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 存取原則和 Azure Resource Manager 範本

當多次使用 Azure Resource Manager 範本來建立工作區和相關聯資源 (包括 Azure Key Vault) 時。 例如，多次使用範本並搭配相同的參數，以作為持續整合和部署管線的一部分。

大部分透過範本的資源建立作業均為等冪，但 Key Vault 會在每次使用範本時清除存取原則。 清除存取原則會針對正在使用它的任何現有工作區，中斷對 Key Vault 的存取權。 例如，Azure Notebooks VM 的停止/建立功能可能會失敗。  

若要避免這個問題，建議採用下列其中一種方法：

* 不要針對相同的參數多次部署範本。 或刪除現有的資源，然後使用範本予以重建。

* 檢查 Key Vault 的存取原則，然後使用這些原則來設定範本的 `accessPolicies` 屬性。 若要檢視存取原則，請使用下列 Azure CLI 命令：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    如需使用範本 `accessPolicies` 區段的詳細資訊，請參閱 [AccessPolicyEntry 物件參考](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)。

* 檢查 Key Vault 資源是否已存在。 如果有，請不要透過範本予以重建。 例如，若要使用現有的 Key Vault，而不是建立新的，請對範本進行下列變更：

    * **新增**可接受現有 Key Vault 資源識別碼的參數：

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **移除**建立 Key Vault 資源的區段：

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * 從工作區的 `dependsOn` 區段**移除** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 行。 此外，**變更**工作區中 `properties` 區段的 `keyVault` 項目，以參考 `keyVaultId` 參數：

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    這些變更之後，即可在執行範本時指定現有 Key Vault 資源的識別碼。 然後範本會將工作區的 `keyVault` 屬性設定為其識別碼，以重複使用 Key Vault。

    若要取得 Key Vault 的識別碼，可參考原始範本執行的輸出，或使用 Azure CLI。 下列命令是使用 Azure CLI 取得 Key Vault 資源識別碼的範例：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    此命令會傳回類似下列文字的值：

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Resource Manager 範本和 Resource Manager REST API 部署資源](../azure-resource-manager/templates/deploy-rest.md)。
* [透過 Visual Studio 建立與部署 Azure 資源群組](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
