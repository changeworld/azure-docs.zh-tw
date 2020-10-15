---
title: 活頁簿的 Resource Manager 範本範例
description: 用來部署 Azure 監視器活頁簿的 Azure Resource Manager 範本範例。
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 05/18/2020
ms.openlocfilehash: 72e6e085135c07e962dea6253af7d40333295a86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83853201"
---
# <a name="resource-manager-template-samples-for-workbooks-in-azure-monitor"></a>Azure 監視器活頁簿適用的 Resource Manager 範本範例
本文包含 [Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)範例，可用來在 Azure 監視器中建立活頁簿。 每個範例都包含範本檔案和參數檔案，且附有要提供給範本的範例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

活頁簿可能很複雜，因此一般的策略是在 Azure 入口網站中建立活頁簿，然後產生 Resource Manager 範本。 如需有關此方法的詳細資料，請參閱[用於部署活頁簿的 Azure Resource Manager 範本](../platform/workbooks-automate.md)。

## <a name="create-a-workbook"></a>建立活頁簿
下列範例會建立簡單的活頁簿。


### <a name="template-file"></a>範本檔案

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="parameter-file"></a>參數檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName": {
            "value": "Sample Hello World workbook"
        },
      "workbookType": {
        "value": "workbook"
      },
      "workbookSourceId": {
        "value": "Azure Monitor"
      }
    }
}
```

## <a name="next-steps"></a>後續步驟

* [取得 Azure 監視器的其他範例範本](resource-manager-samples.md)。
* [深入了解動作群組](../platform/workbooks-overview.md)。
