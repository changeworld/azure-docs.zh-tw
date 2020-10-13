---
title: Azure 監視器活頁簿和 Azure Resource Manager 範本
description: 使用透過 Azure Resource Manager 範本部署的預建和自訂參數化 Azure 監視器活頁簿，簡化複雜的報表
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929722"
---
# <a name="programmatically-manage-workbooks"></a>以程式設計方式管理活頁簿

資源擁有者可以選擇透過 Resource Manager 範本，以程式設計方式建立和管理其活頁簿。

在下列案例中，這會很有用：
* 部署組織或網域專屬的分析報表，以及資源部署。 例如，您可以為新的應用程式或虛擬機器，部署組織特定的效能和失敗活頁簿。
* 使用現有資源的活頁簿部署標準報表或儀表板。

活頁簿將會在所需的子/資源群組中建立，並以 Resource Manager 範本中指定的內容建立。

有兩種類型的活頁簿資源可以透過程式設計方式管理：
* [活頁簿範本](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [活頁簿實例](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>用於部署活頁簿範本的 Azure Resource Manager 範本

1. 開啟您想要以程式設計方式部署的活頁簿。
2. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
3. 使用工具列上的按鈕開啟 _進階編輯器_ _</>_ 。
4. 確定您位於 [主機 _庫範本_ ] 索引標籤上。

    ![資源庫範本索引標籤](./media/workbooks-automate/gallery-template.png)
1. 將資源庫範本中的 JSON 複製到剪貼簿。
2. 以下是 Azure Resource Manager 範本範例，可將活頁簿範本部署至 Azure 監視器活頁簿圖庫。 貼上您所複製的 JSON 來取代 `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` 。 您可以在 [這裡](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template)找到建立活頁簿範本的參考 Azure Resource Manager 範本。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. 在物件中， `galleries` `name` 使用您的值填入和索引 `category` 鍵。 深入瞭解下一節中的 [參數](#parameters) 。
2. 使用 [Azure 入口網站](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)、 [命令列介面](../../azure-resource-manager/templates/deploy-cli.md)、 [PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)等，部署此 Azure Resource Manager 範本。
3. 開啟 Azure 入口網站，然後流覽至 Azure Resource Manager 範本中所選擇的活頁簿圖庫。 在範例範本中，流覽至 Azure 監視器活頁簿圖庫：
    1. 開啟 Azure 入口網站，然後流覽至 Azure 監視器
    2. `Workbooks`從目錄開啟
    3. 在 [類別目錄] 下的資源庫中尋找您的範本 `Deployed Templates` (會是) 的其中一個紫色專案。

### <a name="parameters"></a>參數

|參數                |說明                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Azure Resource Manager 中活頁簿範本資源的名稱。                                  |
|`type`                    | 一律是 microsoft insights/workbooktemplates                                                            |
| `location`               | 將在其中建立活頁簿的 Azure 位置。                                               |
| `apiVersion`             | 2019-10-17 預覽                                                                                     |
| `type`                   | 一律是 microsoft insights/workbooktemplates                                                            |
| `galleries`              | 要在其中顯示此活頁簿範本的資源庫集合。                                                |
| `gallery.name`           | 資源庫中活頁簿範本的易記名稱。                                             |
| `gallery.category`       | 資源庫中用來放置範本的群組。                                                     |
| `gallery.order`          | 決定在資源庫的類別中顯示範本之順序的數位。 較低順序表示優先順序較高。 |
| `gallery.resourceType`   | 對應至資源庫的資源類型。 這通常是對應至資源 (的資源類型字串，例如 operationalinsights/workspace ) 。 |
|`gallery.type`            | 稱為「活頁簿類型」，這是用來區別資源類型內資源庫的唯一索引鍵。 例如，Application Insights 有類型， `workbook` 並 `tsg` 對應至不同的活頁簿資源庫。 |

### <a name="galleries"></a>資源庫

| 主機庫                                        | 資源類型                                      | 活頁簿類型 |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Azure 監視器中的活頁簿                     | `Azure Monitor`                                    | `workbook`    |
| Azure 監視器中的 VM 深入解析                   | `Azure Monitor`                                    | `vm-insights` |
| Log analytics 工作區中的活頁簿           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Application Insights 中的活頁簿              | `microsoft.insights/component`                     | `workbook`    |
| Application Insights 中的疑難排解指南 | `microsoft.insights/component`                     | `tsg`         |
| Application Insights 中的使用方式                  | `microsoft.insights/component`                     | `usage`       |
| Kubernetes 服務中的活頁簿                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| 資源群組中的活頁簿                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Azure Active Directory 中的活頁簿            | `microsoft.aadiam/tenant`                          | `workbook`    |
| 虛擬機器中的 VM 深入解析                | `microsoft.compute/virtualmachines`                | `insights`    |
| 虛擬機器擴展集中的 VM 深入解析      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>用於部署活頁簿實例的 Azure Resource Manager 範本

1. 開啟您想要以程式設計方式部署的活頁簿。
2. 按一下 [ _編輯_ ] 工具列專案，將活頁簿切換至編輯模式。
3. 使用工具列上的按鈕開啟 _進階編輯器_ _</>_ 。
4. 在編輯器中，將 _範本類型_ 切換為 _Resource Manager 範本_。
5. 用於建立的 Resource Manager 範本會顯示在編輯器中。 複製內容並依原樣使用，或將它與同時部署目標資源的大型範本合併。

    ![顯示如何從活頁簿 UI 內取得 Resource Manager 範本的影像](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>範例 Azure Resource Manager 範本
此範本顯示如何部署顯示 ' Hello World！ ' 的簡單活頁簿
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
| `workbookDisplayName` | 資源庫或儲存清單中所使用之活頁簿的易記名稱。 在資源群組和來源的範圍中必須是唯一的 |
| `workbookType` | 活頁簿會顯示在其下的資源庫。 支援的值包括活頁簿、 `tsg` 、Azure 監視器等等。 |
| `workbookSourceId` | 活頁簿將關聯之資源實例的識別碼。 新的活頁簿會顯示此資源實例的相關內容，例如，位於活頁 _簿_下的資源資料表中。 如果您想要在 Azure 監視器的活頁簿圖庫中顯示活頁簿，請使用字串 _Azure 監視器_ ，而不是資源識別碼。 |
| `workbookId` | 此活頁簿實例的唯一 guid。 使用 _[newGuid ( # A1]_ 自動建立新的 guid。 |
| `kind` | 用來指定建立的活頁簿是共用或私用。 使用共用活頁簿的 _共用_ 值和私用活頁簿的 _使用者_ 。 |
| `location` | 將在其中建立活頁簿的 Azure 位置。 使用 _[resourceGroup ( # A1. location]_ 在與資源群組相同的位置中建立它 |
| `serializedData` | 包含要在活頁簿中使用的內容或承載。 使用活頁簿 UI 的 Resource Manager 範本來取得值 |

### <a name="workbook-types"></a>活頁簿類型
活頁簿類型會指定要在哪個活頁簿中顯示新的活頁簿實例。 這些選項包括：

| 類型 | 資源庫位置 |
| :------------- |:-------------|
| `workbook` | 大部分報表中使用的預設值，包括 Application Insights、Azure 監視器等的活頁簿資源庫。  |
| `tsg` | Application Insights 中的疑難排解指南資源庫 |
| `usage` | Application Insights 中_使用_的資源庫_越多_ |

### <a name="limitations"></a>限制
基於技術的原因，此機制無法用來在 Application Insights 的活頁 _簿_ 資源庫中建立活頁簿實例。 我們正在努力解決這項限制。 在此同時，我們建議您使用疑難排解指南庫 (workbookType： `tsg`) 來部署 Application Insights 相關活頁簿。

## <a name="next-steps"></a>後續步驟

探索如何使用活頁簿來增強新 [Azure 監視器的儲存體驗](../insights/storage-insights-overview.md)。
