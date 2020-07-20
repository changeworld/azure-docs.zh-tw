---
title: 使用 REST API 和範本部署資源
description: 使用 Azure Resource Manager 和 Resource Manager REST API 將資源部署到 Azure。 資源會定義在 Resource Manager 範本中。
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: a2280d3bb406fd7e5c41558478363de68cbd44b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84678404"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>使用 ARM 範本和 Resource Manager REST API 部署資源

這篇文章說明如何使用 Resource Manager REST API 與 Azure Resource Manager (ARM) 範本來將您的資源部署至 Azure。

您可以在要求本文中納入範本，也可以連結至檔案。 使用檔案時，該檔案可以是本機檔案，也可以是透過 URI 所取得的外部檔案。 當範本位於儲存體帳戶中時，您可以限制範本的存取權，並在部署期間提供共用存取簽章 (SAS) 權杖。

## <a name="deployment-scope"></a>部署範圍

您可以將部署的目標設定為管理群組、Azure 訂用帳戶或資源群組。 在大部分情況下，您會將部署目標設定為資源群組。 使用管理群組或訂用帳戶部署，在指定的範圍內套用原則和角色指派。 您也可以使用「訂用帳戶」部署來建立資源群組，並將資源部署到其中。 視部署的範圍而定，您可以使用不同的命令。

* 若要部署至**資源群組**，請使用 [[部署 - 建立]](/rest/api/resources/deployments/createorupdate)。 要求會傳送至：

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

* 若要部署至**訂用帳戶**，請使用 [[部署 - 在訂用帳戶範圍建立]](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。 要求會傳送至：

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  如需訂用帳戶層級部署的詳細資訊，請參閱[在訂用帳戶層級建立資源群組和資源](deploy-to-subscription.md)。

* 若要部署到**管理群組**，請使用 [[部署 - 在管理群組範圍建立]](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。 要求會傳送至：

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  如需管理群組層級部署的詳細資訊，請參閱[在管理群組層級建立資源](deploy-to-management-group.md)。

* 若要部署至**租用戶**，請使用 [[部署 - 在租用戶範圍建立或更新]](/rest/api/resources/deployments/createorupdateattenantscope)。 要求會傳送至：

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
  ```

  如需租用戶層級部署的詳細資訊，請參閱[在租用戶層級建立資源](deploy-to-tenant.md)。

本文中的範例會使用資源群組部署。

## <a name="deploy-with-the-rest-api"></a>使用 REST API 部署

1. 設定[一般參數和標頭](/rest/api/azure/) (包括驗證權杖)。

1. 如果您沒有現有資源群組，請建立新的資源群組。 提供您的訂用帳戶識別碼、新資源群組的名稱，以及需要解決方案的位置。 如需詳細資訊，請參閱[建立資源群組](/rest/api/resources/resourcegroups/createorupdate)。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-10-01
   ```

   使用如下的要求本文：

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. 在部署您的範本之前，您可以預覽範本對環境所做的變更。 使用「[假設](template-deploy-what-if.md)」作業來確認範本會進行您預期的變更。 假設也會驗證範本是否有錯誤。

1. 若要部署範本，請在要求 URI 中提供您的訂用帳戶識別碼、資源群組的名稱、部署的名稱。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   在要求本文中，提供您的範本和參數檔案的連結。 如需參數檔案的詳細資訊，請參閱[建立 Resource Manager 參數檔案](parameter-files.md)。

   請注意，**mode** 是設為 **Incremental**。 若要執行完整部署，請將 **mode** 設為 **Complete**。 使用完整模式時請務必謹慎，因為您可能會不小心刪除不在範本中的資源。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    如果您想要記錄回應內容及/或要求內容，可在要求中包括 **debugSetting** 。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    您可以設定儲存體帳戶以使用共用存取簽章 (SAS) Token。 如需詳細資訊，請參閱[使用共用存取簽章委派存取](/rest/api/storageservices/delegating-access-with-a-shared-access-signature)。

    如果您需要提供參數機密的值 (例如密碼)，請將該值加入金鑰保存庫。 在部署期間擷取金鑰保存庫，如先前範例所示。 如需詳細資訊，請參閱 [在部署期間傳遞安全值](key-vault-parameter.md)。

1. 不要連結至含有範本和參數的檔案，而是將它們納入要求本文中。 下列範例顯示內嵌範本和參數的要求本文：

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
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
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. 若要取得範本部署的狀態，請使用 [Deployments - Get](/rest/api/resources/deployments/get)。

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="next-steps"></a>後續步驟

- 在您收到錯誤時，若要回復為成功的部署，請參閱[錯誤回復至成功部署](rollback-on-error.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要了解如何處理非同步 REST 作業，請參閱[追蹤非同步 Azure 作業 (英文)](../management/async-operations.md)。
- 若要深入了解範本，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。

