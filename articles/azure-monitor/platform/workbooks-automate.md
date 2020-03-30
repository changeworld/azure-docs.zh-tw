---
title: Azure 監視活頁簿和 Azure 資源管理器範本
description: 通過通過 Azure 資源管理器範本部署的預構建和自訂參數化 Azure 監視器活頁簿簡化複雜報告
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658399"
---
# <a name="programmatically-manage-workbooks"></a>以程式設計方式管理活頁簿

資源擁有者可以選擇通過資源管理器範本以程式設計方式創建和管理其活頁簿。 

這在以下情況下非常有用：
* 部署特定于組織或域的分析報告以及資源部署。 例如，您可以為新應用或虛擬機器部署特定于組織的性能和故障活頁簿。
* 使用現有資源的活頁簿部署標準報表或儀表板。

活頁簿將在所需的子/資源組中創建，並且使用資源管理器範本中指定的內容創建。

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>用於部署活頁簿的 Azure 資源管理器範本
1. 打開要以程式設計方式部署的活頁簿。
2. 通過按一下 _"編輯_"工具列項，將活頁簿切換到編輯模式。
3. 使用工具列上的_</>_ 按鈕打開_高級編輯器_。
4. 在編輯器中，將_範本類型_切換到_資源管理器範本_。
5. 用於創建的資源管理器範本顯示在編輯器中。 複製內容並按照"自如"使用，或將其與部署目標資源的大型範本合併。

    ![顯示如何從活頁簿 UI 中獲取資源管理器範本的圖像](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>示例 Azure 資源管理器範本
此範本演示如何部署顯示"你好世界！
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

### <a name="template-parameters"></a>範本參數

| 參數 | 說明 |
| :------------- |:-------------|
| `workbookDisplayName` | 在庫或保存清單中使用的活頁簿的易記名稱。 需要在資源組和源的範圍內是唯一的 |
| `workbookType` | 活頁簿下將顯示的庫。 支援的值包括活頁簿、Azure`tsg`監視器等。 |
| `workbookSourceId` | 活頁簿將關聯到的資源實例的 ID。 新的活頁簿將顯示與此資源實例相關 ，例如在_活頁簿_下的資源內容表中。 如果希望活頁簿顯示在 Azure 監視器的活頁簿庫中，請使用字串_Azure 監視器_而不是資源識別碼。 |
| `workbookId` | 此活頁簿實例的唯一 guid。 使用 _[new Guid（）]_ 自動創建新 guid。 |
| `kind` | 用於指定創建的活頁簿是共用的還是私有的。 將_共用_值用於共用活頁簿，將_使用者_共用為私有活頁簿。 |
| `location` | 將創建活頁簿的 Azure 位置。 使用 _[資源組（.位置]）_ 在資源組相同的位置創建它 |
| `serializedData` | 包含要在活頁簿中使用的內容或有效負載。 使用活頁簿 UI 中的資源管理器範本獲取值 |

### <a name="workbook-types"></a>活頁簿類型
活頁簿類型指定新活頁簿實例將在下顯示的活頁簿庫類型。 選項包括：

| 類型 | 畫廊位置 |
| :------------- |:-------------|
| `workbook` | 大多數報表中使用的預設值，包括應用程式見解的活頁簿庫、Azure 監視器等。  |
| `tsg` | 應用程式見解中的故障排除指南庫 |
| `usage` | 應用程式洞察中_使用_下的_更多_庫 |

### <a name="limitations"></a>限制
出於技術原因，此機制不能用於在應用程式見解_的活頁簿_庫中創建活頁簿實例。 我們正在努力解決這一限制。 同時，我們建議您使用故障排除指南庫（活頁簿類型：）`tsg`來部署與應用程式見解相關的活頁簿。

## <a name="next-steps"></a>後續步驟

瞭解如何使用活頁簿為新的[Azure 監視器提供存儲體驗](../insights/storage-insights-overview.md)。

