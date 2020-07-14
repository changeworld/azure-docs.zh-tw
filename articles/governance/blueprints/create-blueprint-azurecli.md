---
title: 快速入門：使用 Azure CLI 建立藍圖
description: 在此快速入門中，您將在 Azure CLI 中使用 Azure 藍圖建立、定義及部署成品。
ms.date: 06/02/2020
ms.topic: quickstart
ms.openlocfilehash: 30a450fc7eab55424da7ce971ad234cbf2248b30
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85969663"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>快速入門：使用 Azure CLI 定義及指派 Azure 藍圖

了解如何建立及指派有助於定義常用模式的藍圖，以根據 Azure Resource Manager 範本 (ARM 範本)、原則、安全性等，開發出可重複使用並可快速部署的組態。 在本教學課程中，您將了解如何使用 Azure 藍圖在您的組織中處理藍圖的建立、發佈和指派等常見工作，例如：

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>新增藍圖延伸模組

若要讓 Azure CLI 可管理藍圖定義與指派，您必須新增延伸模組。
此延伸模組適用於可使用 Azure CLI 的任何地方，包括 [Windows 10 的 Bash](/windows/wsl/install-win10)、[Cloud Shell](https://shell.azure.com) (獨立與內部入口網站)、[Azure CLI Docker 映像](https://hub.docker.com/r/microsoft/azure-cli/)，或在本機安裝。

1. 確認已安裝最新的 Azure CLI (至少 **2.0.76**)。 如果尚未安裝，請依照[這些指示](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)操作。

1. 在您選擇的 Azure CLI 環境中，使用下列命令匯入：

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. 驗證延伸模組已安裝，且為預期的版本 (至少為 **0.1.0**)：

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>建立藍圖

定義合規性標準模式的第一個步驟，即是以可用的資源規劃藍圖。 我們將建立名為 'MyBlueprint' 的藍圖，以設定訂用帳戶的角色和原則指派。 然後，我們將新增資源群組、ARM 範本，以及資源群組的角色指派。

> [!NOTE]
> 使用 Azure CLI 時，會先建立「藍圖」物件。 對於要新增的具有參數的每個_成品_，需要在初始_藍圖_上預先定義參數。

1. 建立初始_藍圖_物件。 **parameters** 參數會接受一個 JSON 檔案，其中包含所有藍圖層級參數。 這些參數會在指派期間設定，並且供後續步驟中新增的成品使用。

   - JSON 檔案 - blueprintparms.json

     ```json
     {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_GRS",
                "Standard_ZRS",
                "Premium_LRS"
            ],
            "metadata": {
                "displayName": "storage account type.",
                "description": null
            }
        },
        "tagName": {
            "type": "string",
            "metadata": {
                "displayName": "The name of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "tagValue": {
            "type": "string",
            "metadata": {
                "displayName": "The value of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "contributors": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > 匯入藍圖定義時，請使用檔案名稱 _blueprint.json_。
     > 呼叫 [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext-blueprint-az-blueprint-import) 時，會使用這個檔案名稱。

     根據預設，藍圖物件會建立在預設訂用帳戶中。 若要指定管理群組，請使用參數 **managementgroup**。 若要指定訂用帳戶，請使用參數 **subscription**。

1. 將儲存體成品的資源群組新增到定義。

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. 在訂用帳戶中新增角色指派。 在下列範例中，授與指定角色的主體身分識別是設定給藍圖指派期間設定的參數。 此範例使用 GUID 為 `b24988ac-6180-42a0-ab88-20f7382dd24c` 的_參與者_內建角色。

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. 在訂用帳戶中新增原則指派。 此範例使用 [將標籤及其預設值套用至資源群組] 內建原則，GUID 為 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`。

   - JSON 檔案 - artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. 在訂用帳戶為儲存標記新增另一個儲存體標記 (重複使用 _storageAccountType_ 參數)。 這個新增的原則指派成品示範藍圖上定義的參數可供多個成品使用。 在此範例中，會使用 **storageAccountType** 來設定資源群組的標記。 此值會提供在下一個步驟中建立的儲存體帳戶的相關資訊。 此範例使用 [將標籤及其預設值套用至資源群組] 內建原則，GUID 為 `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`。

   - JSON 檔案 - artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. 在資源群組下新增範本。 ARM 範本的 **template** 參數包含範本的一般 JSON 元件。 此範本也會將 **storageAccountType**、**tagName** 和 **tagValue** 藍圖參數傳至範本，以重複使用這些參數。 藍圖參數可透過使用 **parameters** 參數提供給範本使用，而且可在使用機碼值組來插入值的 JSON 範本內使用。 藍圖和範本參數名稱可能相同。

   - JSON ARM 範本檔案 - artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON ARM 範本參數檔案 - artifacts\templateStorageParams.json

     ```json
     {
        "storageAccountTypeFromBP": {
           "value": "[parameters('storageAccountType')]"
        },
        "tagNameFromBP": {
           "value": "[parameters('tagName')]"
        },
        "tagValueFromBP": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. 在資源群組下新增角色指派。 類似於先前的角色指派項目，下列範例為**擁有者**角色使用定義識別碼，並為它提供藍圖的不同參數。 此範例使用 GUID 為 `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` 的_擁有者_內建角色。

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>發佈藍圖

將成品新增到藍圖之後，即可發佈藍圖。 藍圖發佈後即可指派給訂用帳戶。

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

`{BlueprintVersion}` 的值是字母、數字和連字號組成的字串 (無空格或其他特殊字元)，最大長度為 20 個字元。 使用如 **v20200605-135541** 這類唯一且包含資訊的字串。

## <a name="assign-a-blueprint"></a>指派藍圖

使用 Azure CLI 發佈藍圖後，即可將藍圖指派給訂用帳戶。 將您建立的藍圖指派給管理群組階層下的其中一個訂用帳戶。 如果將藍圖儲存到某訂用帳戶，則只能將其指派給該訂用帳戶。 **blueprint-name** 參數可指定要指派的藍圖。 若要提供名稱、位置、身分識別、鎖定與藍圖參數，請在 `az blueprint assignment create` 命令上使用相符的 Azure CLI 參數，或在 **parameters** 參數 JSON 檔案中提供這些參數。

1. 將藍圖部署指派給訂用帳戶以執行它。 **contributors** 與 **owners** 參數需要主體的 objectId 陣列以對其授與角色指派，因此請使用 [Azure Active Directory 圖形 API](../../active-directory/develop/active-directory-graph-api.md) 收集 objectId 以用於您自己的使用者、群組或服務主體的 **parameters** 中。

   - JSON 檔案 - blueprintAssignment.json

     ```json
     {
        "storageAccountType": {
            "value": "Standard_GRS"
        },
        "tagName": {
            "value": "CostCenter"
        },
        "tagValue": {
            "value": "ContosoIT"
        },
        "contributors": {
            "value": [
                "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                "38833b56-194d-420b-90ce-cff578296714"
            ]
        },
        "owners": {
            "value": [
                "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                "316deb5f-7187-4512-9dd4-21e7798b0ef9"
            ]
        }
     }
     ```

   - Azure CLI 命令

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - 使用者指派的受控識別

     藍圖指派也可以使用[指派使用者的受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。
     在此情況下，**identity-type** 參數會設定為 _UserAssigned_，而 **user-assigned-identities** 參數會指定身分識別。 請將 `{userIdentity}` 取代為您的使用者指派受控識別名稱。

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     **使用者指派的受控識別**可位於使用者指派藍圖具有權限的任何訂用帳戶和資源群組中。

     > [!IMPORTANT]
     > Azure 藍圖不會管理使用者指派的受控識別。 使用者需負責指派足夠的角色和權限，否則藍圖指派將會失敗。

## <a name="clean-up-resources"></a>清除資源

### <a name="unassign-a-blueprint"></a>取消指派藍圖

您可以從訂用帳戶中移除藍圖。 移除作業通常會在成品資源已不再需要時執行。 移除藍圖時，會將指派為該藍圖一部份的成品保留下來。 若要移除藍圖指派，請使用 `az blueprint assignment delete` 命令：

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已使用 Azure CLI 建立、指派及移除藍圖。 若要深入了解 Azure 藍圖，請繼續閱讀藍圖生命週期文章。

> [!div class="nextstepaction"]
> [了解藍圖生命週期](./concepts/lifecycle.md)