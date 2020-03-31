---
title: 使用 REST API 和範本部署資源
description: 使用 Azure 資源管理器和資源管理器 REST API 將資源部署到 Azure。 資源會定義在 Resource Manager 範本中。
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153228"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>使用 ARM 範本和資源管理器 REST API 部署資源

本文介紹如何使用資源管理器 REST API 與 Azure 資源管理器 （ARM） 範本一起將資源部署到 Azure。

您可以在要求本文中納入範本，也可以連結至檔案。 使用檔案時，該檔案可以是本機檔案，也可以是透過 URI 所取得的外部檔案。 當範本位於儲存體帳戶中時，您可以限制範本的存取權，並在部署期間提供共用存取簽章 (SAS) 權杖。

## <a name="deployment-scope"></a>部署範圍

您可以將部署定位為管理組、Azure 訂閱或資源組。 在大多數情況下，您將將部署目標鎖定到資源組。 使用管理組或訂閱部署在指定的範圍內應用策略和角色指派。 您還可以使用訂閱部署創建資源組並將資源部署到該資源。 根據部署的範圍，使用不同的命令。

要部署到**資源組**，請使用["部署 - 創建](/rest/api/resources/deployments/createorupdate)"。 要求傳送到：

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

要部署到**訂閱**，請使用部署[- 在訂閱範圍內創建](/rest/api/resources/deployments/createorupdateatsubscriptionscope)。 要求傳送到：

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

有關訂閱級別部署的詳細資訊，請參閱[在訂閱級別創建資源組和資源](deploy-to-subscription.md)。

要部署到**管理組**，請使用部署[- 在管理組作用域中創建](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)。 要求傳送到：

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

有關管理組級別部署的詳細資訊，請參閱[在管理組級別創建資源](deploy-to-management-group.md)。

本文中的示例使用資源組部署。

## <a name="deploy-with-the-rest-api"></a>使用 REST API 部署

1. 設定[一般參數和標頭](/rest/api/azure/) (包括驗證權杖)。

1. 如果您沒有現有資源群組，請建立新的資源群組。 提供您的訂用帳戶識別碼、新資源群組的名稱，以及需要解決方案的位置。 如需詳細資訊，請參閱[建立資源群組](/rest/api/resources/resourcegroups/createorupdate)。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
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

1. 請先執行[驗證範本部署作業](/rest/api/resources/deployments/validate)來驗證部署，然後再執行部署。 測試部署時，請提供與執行部署時完全一致的參數 (如下個步驟所示)。

1. 要部署範本，請提供訂閱 ID、資源組的名稱、請求 URI 中的部署名稱。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   在請求正文中，提供指向範本和參數檔的連結。 有關參數檔的詳細資訊，請參閱[創建資源管理器參數檔](parameter-files.md)。

   請注意，**mode** 是設為 **Incremental**。 要運行完全部署，將**模式**設置為 **"完成**"。 使用完整模式時請務必謹慎，因為您可能會不小心刪除不在範本中的資源。

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

    如果您需要提供參數機密的值 (例如密碼)，請將該值加入金鑰保存庫。 在部署期間擷取金鑰保存庫，如先前範例所示。 有關詳細資訊，請參閱[在部署期間傳遞安全值](key-vault-parameter.md)。

1. 不要連結至含有範本和參數的檔案，而是將它們納入要求本文中。 下面的示例顯示具有範本和參數內聯的請求正文：

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

1. 要獲取範本部署的狀態，請使用["部署 - 獲取](/rest/api/resources/deployments/get)"。

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>後續步驟

- 要在收到錯誤時回滾到成功部署，請參閱[在錯誤時回滾到成功部署](rollback-on-error.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要了解如何處理非同步 REST 作業，請參閱[追蹤非同步 Azure 作業 (英文)](../management/async-operations.md)。
- 要瞭解有關範本的更多資訊，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。

