---
title: 視圖定義概述
description: 描述為 Azure 託管應用程式創建視圖定義的概念。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: d0c60f5738bf634f9d43d6d4f0d78c1239b7ff3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650691"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>在 Azure 託管應用程式中查看定義專案

視圖定義是 Azure 託管應用程式中的可選項目。 它允許自訂概覽頁面並添加更多視圖，如指標和自訂資源。

本文概述了視圖定義工件及其功能。

## <a name="view-definition-artifact"></a>檢視定義成品

視圖定義專案必須命名為**viewDefinition.json，** 並將其放置在創建託管應用程式的 .zip 包中的**createUiDefinition.json**和**mainTemplate.json**相同的級別。 要瞭解如何創建 .zip 包併發布託管應用程式定義，請參閱發佈[Azure 託管應用程式定義](publish-managed-app-definition-quickstart.md)

## <a name="view-definition-schema"></a>查看定義架構

**viewDefinition.json**檔只有一個頂級`views`屬性，這是一個視圖陣列。 每個視圖在託管應用程式使用者介面中顯示為目錄中的單獨功能表項目。 每個視圖都有一`kind`個屬性，該屬性設置視圖的類型。 它必須設置為以下值之一：[概述](#overview)、[指標](#metrics)、[自訂資源](#custom-resources)、[關聯](#associations)。 有關詳細資訊，請參閱[當前 JSON 架構，瞭解視圖定義.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

用於視圖定義的 JSON 示例：

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

## <a name="overview"></a>總覽

`"kind": "Overview"`

當您在**viewDefinition.json**中提供此視圖時，它將覆蓋託管應用程式中的預設"概述"頁。

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
|命令|否|概述頁的其他工具列按鈕的陣列，請參閱[命令](#commands)。|

![總覽](./media/view-definition/overview.png)

## <a name="metrics"></a>計量

`"kind": "Metrics"`

指標視圖使您能夠在[Azure 監視器指標](../../azure-monitor/platform/data-platform-metrics.md)中從託管應用程式資源收集和聚合資料。

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
|圖表|是|指標頁圖表陣列。|

### <a name="chart"></a>圖表

|屬性|必要|描述|
|---------|---------|---------|
|displayName|是|圖表的顯示標題。|
|圖表類型|否|用於此圖表的視覺化效果。 預設情況下，它使用折線圖。 支援的圖表類型： `Bar, Line, Area, Scatter`.|
|metrics|是|要在此圖表上繪製的指標陣列。 要瞭解有關 Azure 門戶中支援指標的更多內容，請參閱[使用 Azure 監視器支援指標](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>計量

|屬性|必要|描述|
|---------|---------|---------|
|NAME|是|計量的名稱。|
|聚合類型|是|用於此指標的聚合類型。 支援的聚合類型：`none, sum, min, max, avg, unique, percentile, count`|
|namespace|否|在確定正確的指標提供程式時要使用的其他資訊。|
|資源標籤篩選器|否|將顯示指標的資源標記陣列（將用`or`單詞分隔）。 應用於資源類型篩選器的頂部。|
|resourceType|是|將顯示指標的資源類型。|

![計量](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>自訂資源

`"kind": "CustomResources"`

您可以定義此類型的多個視圖。 每個視圖表示在**mainTemplate.json**中定義的自訂提供程式**中的唯**一自訂資源類型。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](../custom-providers/overview.md)\(英文\)。

在此視圖中，您可以為自訂資源類型執行 GET、PUT、DELETE 和 POST 操作。 POST 操作可以是自訂資源類型的上下文中的全域自訂操作或自訂操作。

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
|displayName|是|視圖的顯示標題。 標題對於**視圖定義.json**中的每個自訂資源檢視應**是唯一**的。|
|version|否|用於呈現視圖的平臺的版本。|
|resourceType|是|自訂資源類型。 必須是自訂提供程式**的唯**一自訂資源類型。|
|icon|否|視圖的圖示。 示例圖示清單在[JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定義。|
|創建 UI 定義|否|創建用於創建自訂資源命令的 UI 定義架構。 有關創建 UI 定義的簡介，請參閱[開始創建 Ui 定義](create-uidefinition-overview.md)|
|命令|否|自訂資源檢視的其他工具列按鈕的陣列，請參閱[命令](#commands)。|
|資料行|否|自訂資源的列陣列。 如果未定義，`name`則預設情況下將顯示該列。 列必須具有`"key"`和`"displayName"`。 對於鍵，請提供要在視圖中顯示的屬性的鍵。 如果嵌套，請使用點作為分隔符號，例如，`"key": "name"`或`"key": "properties.property1"`。 對於顯示名稱，請提供要在視圖中顯示的屬性的顯示名稱。 您還可以提供屬性`"optional"`。 當設置為 true 時，預設情況下該列將隱藏在視圖中。|

![CustomResources](./media/view-definition/customresources.png)

## <a name="commands"></a>命令

命令是頁面上顯示的其他工具列按鈕的陣列。 每個命令表示在**mainTemplate.json**中定義的 Azure 自訂提供程式的 POST 操作。 有關自訂提供程式的簡介，請參閱[Azure 自訂提供程式概述](../custom-providers/overview.md)。

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
|path|是|自訂提供程式操作名稱。 操作必須在**主範本.json**中定義。|
|icon|否|命令按鈕的圖示。 示例圖示清單在[JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定義。|
|創建 UI 定義|否|為命令創建 UI 定義架構。 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。|

## <a name="associations"></a>關聯

`"kind": "Associations"`

您可以定義此類型的多個視圖。 此視圖允許您通過**在 mainTemplate.json**中定義的自訂提供程式將現有資源連結到託管應用程式。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](../custom-providers/overview.md)\(英文\)。

在此視圖中，可以基於 擴展`targetResourceType`現有的 Azure 資源。 選擇資源後，它將創建一個對**公共**自訂提供程式的載入請求，該請求可以應用於資源的副作用。 

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
|displayName|是|視圖的顯示標題。 標題對於**視圖定義.json**中的每個相關檢視應**是唯一**的。|
|version|否|用於呈現視圖的平臺的版本。|
|targetResourceType|是|目標資源類型。 這是將顯示的資源類型，用於資源載入。|
|創建 UI 定義|否|創建用於創建關聯資源命令的 UI 定義架構。 有關創建 UI 定義的簡介，請參閱[開始創建 Ui 定義](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>尋求協助

如果您對 Azure 受控應用程式有任何疑問，請嘗試在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-managedapps)提問。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 `azure-managedapps` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

- 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](overview.md)。
- 有關自訂提供程式的簡介，請參閱[Azure 自訂提供程式概述](../custom-providers/overview.md)。
- 有關使用 Azure 自訂提供程式創建 Azure 託管應用程式，請參閱[教程：使用自訂提供程式操作和資源類型創建託管應用程式](tutorial-create-managed-app-with-custom-provider.md)
