---
title: 使用 Azure Resource Manager 範本建立工作區
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Resource Manager 範本來建立新的 Azure Machine Learning 工作區。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 568bcdcfd8ae50fff58964ecc74176b151db22a4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121315"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>使用 Azure Resource Manager 範本來建立 Azure Machine Learning 的工作區

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

在本文中，您將瞭解使用 Azure Resource Manager 範本建立 Azure Machine Learning 工作區的數種方式。 Resource Manager 範本可讓您輕鬆地以單一、協調的作業建立資源。 範本是 JSON 文件，其定義部署所需的資源。 它也可以指定部署參數。 參數用來在使用範本時提供輸入值。

如需詳細資訊，請參閱 [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="prerequisites"></a>先決條件

* **Azure 訂**用帳戶。 如果您沒有帳戶，請試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 若要從 CLI 使用範本，您需要 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 或 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="resource-manager-template"></a>Resource Manager 範本

下列 Resource Manager 範本可以用來建立 Azure Machine Learning 工作區和相關聯的 Azure 資源：

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
> 雖然與此檔相關聯的範本會建立新的 Azure Container Registry，但您也可以建立新的工作區，而不需要建立容器登錄。 當您執行需要 container registry 的作業時，將會建立一個。 例如，定型或部署模型。
>
> 您也可以在 [Azure Resource Manager] 範本中參考現有的容器登錄或儲存體帳戶，而不是建立一個新的。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

如需範本的詳細資訊，請參閱下列文章：

* [編寫 Azure Resource Manager 範本](../azure-resource-manager/templates/template-syntax.md)
* [使用 Azure Resource Manager 範本部署應用程式](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices resource 類型](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Advanced 範本

下列範例範本示範如何建立具有三個設定的工作區：

* 啟用工作區的高機密性設定
* 啟用工作區的加密
* 使用現有的 Azure Key Vault 來取出客戶管理的金鑰

如需詳細資訊，請參閱[靜態加密](concept-enterprise-security.md#encryption-at-rest)。

> [!IMPORTANT]
> 使用此範本之前，您的訂用帳戶必須符合一些特定需求：
> * __Azure Machine Learning__應用程式必須是您 Azure 訂用帳戶的__參與者__。
> * 您必須具有包含加密金鑰的現有 Azure Key Vault。
> * 您在 Azure Key Vault 中必須擁有存取原則，才能授與__Azure Cosmos DB__應用程式的__get__、wrap 和解除__包裝____存取權__。
> * Azure Key Vault 必須位於您打算建立 Azure Machine Learning 工作區的相同區域中。
> * 您的訂用帳戶必須支援__客戶管理的金鑰__以進行 Azure Cosmos DB。

__若要將 Azure Machine Learning 應用程式新增為參與者__，請使用下列命令：

1. 若要從 CLI 向 Azure 進行驗證，請使用下列命令：

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. 若要取得 Azure Machine Learning 應用程式的物件識別碼，請使用下列命令。 每個 Azure 訂用帳戶的值可能不同：

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    此命令會傳回物件識別碼，也就是 GUID。

1. 若要將物件識別碼新增為訂用帳戶的參與者，請使用下列命令。 `<object-ID>`將取代為上一個步驟中的 GUID。 將取代 `<subscription-ID>` 為您的 Azure 訂用帳戶名稱或識別碼：

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__若要將金鑰新增至您的 Azure Key Vault__，請使用使用__Azure CLI 來管理 Key Vault__一文中的將[金鑰、秘密或憑證新增至金鑰保存庫](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault)一節中的資訊。

__若要將存取原則新增至金鑰保存庫，請使用下列命令__：

1. 若要取得 Azure Cosmos DB 應用程式的物件識別碼，請使用下列命令。 每個 Azure 訂用帳戶的值可能不同：

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    此命令會傳回物件識別碼，也就是 GUID。

1. 若要設定原則，請使用下列命令。 取代 `<keyvault-name>` 為現有 Azure Key Vault 的名稱。 `<object-ID>`將取代為上一個步驟中的 GUID：

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__若要為 Azure Cosmos DB 啟用客戶管理的金鑰__，請 azurecosmosdbcmk@service.microsoft.com 使用您的 Azure 訂用帳戶識別碼傳送電子郵件給。 如需詳細資訊，請參閱[為您的 Azure Cosmos 帳戶設定客戶管理的金鑰](..//cosmos-db/how-to-setup-cmk.md)。

__若要取得__ `cmk_keyvault` 此範本所需之（Key Vault 的識別碼）和 `resource_cmk_uri` （金鑰 URI）參數的值，請使用下列步驟：

1. 若要取得 Key Vault 識別碼，請使用下列命令：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    此命令會傳回如下值：`/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault`。

1. 若要取得客戶管理之金鑰的 URI 值，請使用下列命令：

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    此命令會傳回如下值：`https://mykeyvault.vault.azure.net/keys/mykey/{guid}`。

__範例範本__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> 建立工作區之後，您就無法變更機密資料、加密、金鑰保存庫識別碼或金鑰識別元的設定。 若要變更這些值，您必須使用新的值來建立新的工作區。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 遵循[從自訂範本部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)的步驟。 當您看見 [編輯範本]____ 畫面時，貼入此文件的範本。
1. 選取 [儲存]____ 以使用範本。 提供下列資訊，並同意列出的條款及條件：

   * 訂用帳戶：選取要用於這些資源的 Azure 訂用帳戶。
   * 資源群組：選取或建立包含服務的資源群組。
   * 工作區名稱：要用於將建立之 Azure Machine Learning 工作區的名稱。 工作區名稱必須介於 3 到 33 個字元之間。 只能包含英數字元和 '-'。
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

當您使用 Azure Resource Manager 範本來建立工作區和相關聯的資源（包括 Azure Key Vault）時，會多次。 例如，使用範本多次，並以相同的參數作為持續整合和部署管線的一部分。

大部分透過範本的資源建立作業都是等冪的，但 Key Vault 會在每次使用範本時清除存取原則。 清除存取原則會中斷對使用它之任何現有工作區的 Key Vault 存取。 例如，Azure Notebooks VM 的停止/建立功能可能會失敗。  

若要避免這個問題，建議您採用下列其中一種方法：

* 不要針對相同的參數多次部署範本。 或刪除現有的資源，然後再使用此範本重新建立它們。

* 檢查 Key Vault 的存取原則，然後使用這些原則來設定 `accessPolicies` 範本的屬性。 若要查看存取原則，請使用下列 Azure CLI 命令：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    如需使用 `accessPolicies` 範本區段的詳細資訊，請參閱[AccessPolicyEntry 物件參考](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)。

* 檢查 Key Vault 資源是否已存在。 如果有，請不要透過範本重新建立它。 例如，若要使用現有的 Key Vault，而不是建立新的，請對範本進行下列變更：

    * **新增**可接受現有 KEY VAULT 資源識別碼的參數：

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

    * **Remove** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` 從工作區的區段中移除該行 `dependsOn` 。 此外，也請**變更** `keyVault` `properties` 工作區區段中的專案，以參考 `keyVaultId` 參數：

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

    這些變更之後，您可以在執行範本時指定現有 Key Vault 資源的識別碼。 然後範本會將 `keyVault` 工作區的屬性設為其識別碼，以重複使用 Key Vault。

    若要取得 Key Vault 的識別碼，您可以參考原始範本執行的輸出，或使用 Azure CLI。 下列命令是使用 Azure CLI 取得 Key Vault 資源識別碼的範例：

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    此命令會傳回類似下列文字的值：

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Resource Manager 範本和 Resource Manager REST API 來部署資源](../azure-resource-manager/templates/deploy-rest.md)。
* [透過 Visual Studio 建立和部署 Azure 資源群組](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)。
