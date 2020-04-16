---
title: 檢視定義概述
description: 描述為 Azure 託管應用程式創建檢視定義的概念。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391700"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>在 Azure 託管應用程式中檢視定義項目

視圖定義是 Azure 託管應用程式中的可選專案。 它允許自定義概覽頁面並添加更多檢視,如指標和自定義資源。

本文概述了視圖定義工件及其功能。

## <a name="view-definition-artifact"></a>檢視定義成品

視圖定義項目必須命名為**viewDefinition.json,** 並將其放置在創建託管應用程式的 .zip 包中的**createUiDefinition.json**和**mainTemplate.json**相同的級別。 要瞭解如何建立 .zip 包併發佈託管應用程式定義,請參閱發布[Azure 託管應用程式定義](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>檢視定義架構

**viewDefinition.json**檔只有一個`views`頂級 屬性,這是一個視圖陣列。 每個檢視在託管應用程式用戶介面中顯示為目錄中的單獨功能表項。 每個檢視都有一`kind`個屬性,該屬性設置視圖的類型。 這個必須設定為以下值之一: 概述、 指標, 您[Associations](#associations)必須設定與以下值的一: 概述, 單位, 您必須設定為以下值的一: 概述、 指標, 您必須設定為以下值的一:[概述](#overview)、[指標](#metrics),[您必須](#custom-resources)設定為以下值 有關詳細資訊,請參閱[當前 JSON 架構,瞭解檢視定義.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

檢視定義的 JSON 範例:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>概觀

`"kind": "Overview"`

當您在**viewDefinition.json**中提供此檢視時,它將覆蓋託管應用程式中的預設「概述」頁。

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|屬性|必要|描述|
|---------|---------|---------|
|頁首|否|概述頁的標題。|
|description|否|託管應用程式的說明。|
|命令|否|概述頁的其他工具列按鈕的陣列,請參閱[命令](#commands)。|

![概觀](./media/view-definition/overview.png)

## <a name="metrics"></a>計量

`"kind": "Metrics"`

指標檢視使您能夠在[Azure 監視器指標](../../azure-monitor/platform/data-platform-metrics.md)中從託管應用程式資源收集和聚合數據。

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|屬性|必要|描述|
|---------|---------|---------|
|displayName|否|視圖的顯示標題。|
|version|否|用於呈現視圖的平臺的版本。|
|圖表|是|指標頁圖表陣組。|

### <a name="chart"></a>圖表

|屬性|必要|描述|
|---------|---------|---------|
|displayName|是|圖表的顯示標題。|
|圖表類型|否|用於此圖表的可視化效果。 默認情況下,它使用折線圖。 支援的圖表型態`Bar, Line, Area, Scatter`: .|
|metrics|是|要在此圖表上繪製的指標陣列。 要瞭解有關 Azure 門戶中支援指標的更多內容,請參閱[使用 Azure 監視器支援指標](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>計量

|屬性|必要|描述|
|---------|---------|---------|
|NAME|是|計量的名稱。|
|彙總型態|是|用於此指標的聚合類型。 支援的集合模式:`none, sum, min, max, avg, unique, percentile, count`|
|命名空間|否|在確定正確的指標提供程式時要使用的其他資訊。|
|資源標籤篩選器|否|將顯示指標的資源標記陣列(將用`or`單詞分隔)。 應用於資源類型篩選器的頂部。|
|resourceType|是|將顯示指標的資源類型。|

![計量](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>自訂資源

`"kind": "CustomResources"`

您可以定義此類型的多個檢視。 每個檢視表示在**mainTemplate.json**中定義的自定義提供程式**中的唯**一自訂資源類型。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](../custom-providers/overview.md)\(英文\)。

在此檢視中,您可以為自訂資源類型執行 GET、PUT、DELETE 和 POST 操作。 POST 操作可以是自定義資源類型的上下文中的全域自定義操作或自定義操作。

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|屬性|必要|描述|
|---------|---------|---------|
|displayName|是|視圖的顯示標題。 標題對於**檢視定義.json**中的每個自定義資源視圖應**是唯一**的。|
|version|否|用於呈現視圖的平臺的版本。|
|resourceType|是|自定義資源類型。 必須是自定義提供者**的唯**一自定義資源類型。|
|icon|否|視圖的圖示。 示例圖示清單在[JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定義。|
|建立 UI 定義|否|創建用於創建自定義資源命令的 UI 定義架構。 有關建立 UI 定義的簡介,請參閱[開始建立 Ui 定義](create-uidefinition-overview.md)|
|命令|否|自訂資源檢視的其他工具列按鈕的陣列,請參閱[命令](#commands)。|
|資料行|否|自定義資源的列陣列。 如果未定義,`name`則預設情況下將顯示該欄。 欄必須具有`"key"`與`"displayName"`。 對於鍵,請提供要在檢視中顯示的屬性的鍵。 如果嵌入點,請使用點做為分隔符,`"key": "name"`例如`"key": "properties.property1"`, 或 。 對於顯示名稱,請提供要在檢視中顯示的屬性的顯示名稱。 您還可以提供屬性`"optional"`。 當設置為 true 時,默認情況下該列將隱藏在檢視中。|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>命令

命令是頁面上顯示的其他工具列按鈕的陣列。 每個命令表示在**mainTemplate.json**中定義的 Azure 自定義提供程式的 POST 操作。 有關自訂提供者的簡介,請參閱[Azure 自訂提供程式概述](../custom-providers/overview.md)。

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|屬性|必要|描述|
|---------|---------|---------|
|displayName|是|命令按鈕的顯示名稱。|
|path|是|自定義提供程式操作名稱。 操作必須在**主範本.json**中定義。|
|icon|否|命令按鈕的圖示。 示例圖示清單在[JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定義。|
|建立 UI 定義|否|為命令創建 UI 定義架構。 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。|

## <a name="associations"></a>關聯

`"kind": "Associations"`

您可以定義此類型的多個檢視。 此檢視允許您透過**在mainTemplate.json**中定義的自訂提供程式將現有資源連結到託管應用程式。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](../custom-providers/overview.md)\(英文\)。

在此檢視中,可以基於擴展`targetResourceType`現有的 Azure 資源。 選擇資源後,它將創建一個對**公共**自定義提供程式的載入請求,該請求可以應用於資源的副作用。 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|屬性|必要|描述|
|---------|---------|---------|
|displayName|是|視圖的顯示標題。 標題對於**檢視定義.json**中的每個關聯視圖應**是唯一**的。|
|version|否|用於呈現視圖的平臺的版本。|
|targetResourceType|是|目標資源類型。 這是將顯示的資源類型,用於資源載入。|
|建立 UI 定義|否|創建用於創建關聯資源命令的 UI 定義架構。 有關建立 UI 定義的簡介,請參閱[開始建立 Ui 定義](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>尋求協助

如果您對 Azure 受控應用程式有任何疑問，請嘗試在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-managedapps)提問。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 `azure-managedapps` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

- 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](overview.md)。
- 有關自訂提供者的簡介,請參閱[Azure 自訂提供程式概述](../custom-providers/overview.md)。
- 有關使用 Azure 自訂提供程式建立 Azure 託管應用程式,請參閱[教學:使用自訂提供者操作和資源類型建立託管應用程式](tutorial-create-managed-app-with-custom-provider.md)
