---
title: 教學課程 - 資源上線
description: 透過自訂提供者設定資源上線，可讓您操作及擴充現有的 Azure 資源。
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75648605"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>教學課程：透過 Azure 自訂提供者設定資源上線

在本教學課程中，您將對 Azure 部署自訂資源提供者，以使用 Microsoft.CustomProviders/associations 資源類型來擴充 Azure Resource Manager API。 本教學課程會示範如何在自訂提供者執行個體所在的資源群組外部，擴充現有資源。 在本教學課程中，自訂資源提供者是由 Azure 邏輯應用程式提供支援，但是您可以使用任何公用 API 端點。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您必須了解：

* [Azure 自訂提供者](overview.md)的功能。
* [透過自訂提供者設定資源上線](concepts-resource-onboarding.md)的基本資訊。

## <a name="get-started-with-resource-onboarding"></a>開始使用資源上線

在本教學課程中，有兩個部分需要部署：自訂提供者和關聯。 若要讓流程變得更簡單，您可以選擇使用單一範本來部署這兩個項目。

此範本會使用這些資源：

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>部署自訂提供者基礎結構

範本的第一個部分會部署自訂提供者基礎結構。 此基礎結構會定義關聯資源的影響。 如果您不熟悉自訂提供者，請參閱[自訂提供者基本概念](overview.md)。

讓我們開始來部署自訂提供者基礎結構。 您可以複製、儲存及部署上述範本，或使用 Azure 入口網站來遵循並部署基礎結構。

1. 移至 [Azure 入口網站](https://portal.azure.com)。

2. 在 [所有服務]  中搜尋**範本**，或使用主要搜尋方塊來搜尋：

   ![搜尋範本](media/tutorial-resource-onboarding/templates.png)

3. 在 [範本]  窗格中，選取 [新增]  ：

   ![選取 [新增]](media/tutorial-resource-onboarding/templatesadd.png)

4. 在 [一般]  底下，輸入新範本的**名稱**和**描述**：

   ![範本名稱和描述](media/tutorial-resource-onboarding/templatesdescription.png)

5. 從本文的「開始使用資源上線」一節中複製 JSON 範本，以建立 Resource Manager 範本：

   ![建立 Resource Manager 範本](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. 選取 [新增]  以建立範本。 如果未出現新範本，請選取 [重新整理]  。

7. 選取新建立的範本，然後選取 [部署]  ：

   ![選取新範本，然後選取 [部署]](media/tutorial-resource-onboarding/templateselectspecific.png)

8. 輸入必要欄位的設定，然後選取訂用帳戶和資源群組。 您可以將 [自訂資源提供者識別碼]  方塊保留空白。

   | 設定名稱 | 必要項？ | 描述 |
   | ------------ | -------- | ----------- |
   | Location | 是 | 範本中資源的位置。 |
   | 邏輯應用程式名稱 | 否 | 邏輯應用程式的名稱。 |
   | 自訂資源提供者名稱 | 否 | 自訂資源提供者名稱。 |
   | 自訂資源提供者識別碼 | 否 | 支援關聯資源的現有自訂資源提供者。 如果您在此指定值，則會略過邏輯應用程式和自訂提供者部署。 |
   | 關聯名稱 | 否 | 關聯資源的名稱。 |

   範例參數：

   ![輸入範本參數](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. 移至部署並等候部署完成。 您應該會看到類似於下列螢幕擷取畫面的內容。 您應該會看到作為輸出的新關聯資源：

   ![部署成功](media/tutorial-resource-onboarding/customproviderdeployment.png)

   以下是資源群組，其中已選取 [顯示隱藏的類型]  ：

   ![自訂提供者部署](media/tutorial-resource-onboarding/showhidden.png)

10. 瀏覽邏輯應用程式的 [執行記錄]  索引標籤，以查看用於建立關聯的呼叫：

    ![邏輯應用程式執行記錄](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>部署其他關聯

設定好自訂提供者基礎結構之後，您就可以輕鬆地部署更多關聯。 其他關聯的資源群組不一定要與您部署自訂提供者基礎結構的資源群組相同。 若要建立關聯，您必須在指定的自訂資源提供者識別碼上具有 Microsoft.CustomProviders/resourceproviders/write 權限。

1. 在先前部署的資源群組中，移至自訂提供者 **Microsoft.CustomProviders/resourceProviders** 資源。 您必須選取 [顯示隱藏的類型]  核取方塊：

   ![移至資源](media/tutorial-resource-onboarding/showhidden.png)

2. 複製自訂提供者的資源識別碼。

3. 在 [所有服務]  中搜尋**範本**，或使用主要搜尋方塊來搜尋：

   ![搜尋範本](media/tutorial-resource-onboarding/templates.png)

4. 選取先前建立的範本，然後選取 [部署]  ：

   ![選取先前建立的範本，然後選取 [部署]](media/tutorial-resource-onboarding/templateselectspecific.png)

5. 輸入必要欄位的設定，然後選取訂用帳戶和不同的資源群組。 針對 [自訂資源提供者識別碼]  設定，輸入您稍早部署自訂提供者時複製的資源識別碼。

6. 移至部署並等候部署完成。 現在應該只有新的關聯資源會進行部署：

   ![新增關聯資源](media/tutorial-resource-onboarding/createdassociationresource.png)

如有需要，您可以回到邏輯應用程式的**執行記錄**，並查看是否已對邏輯應用程式進行另一個呼叫。 您可以更新邏輯應用程式，為每個建立的關聯增加額外的功能。

## <a name="getting-help"></a>取得說明

如果您對 Azure 自訂提供者有任何疑問，請嘗試在 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers) 上提問。 類似的問題可能已有人回答，因此請先查看再張貼問題。 新增標記 `azure-custom-providers` 以取得快速回應！

