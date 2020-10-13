---
title: 視圖定義總覽
description: 描述為 Azure 受控應用程式建立視圖定義的概念。
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: bff846b4b64778d5e40ea7f08f88faf3dde81d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371604"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Azure 受控應用程式中的 View definition 成品

View definition 是 Azure 受控應用程式中的選擇性成品。 它可讓您自訂總覽頁面，以及新增更多視圖，例如度量和自訂資源。

本文提供 view definition 成品及其功能的總覽。

## <a name="view-definition-artifact"></a>檢視定義成品

視圖定義成品必須命名為**viewDefinition.json** ，並放在建立受控應用程式定義的 .zip 封裝中，並置於與**mainTemplate.js** **createUiDefinition.js**相同的層級上。 若要瞭解如何建立 .zip 套件及發佈受控應用程式定義，請參閱 [發佈 Azure 受控應用程式定義](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>視圖定義架構

檔案 ** 上的viewDefinition.js** 只能有一個最上層 `views` 的屬性，也就是一組 views。 每個視圖都會在 [managed 應用程式] 使用者介面中顯示為目錄中的個別功能表項目。 每個視圖都有一個 `kind` 屬性，可設定視圖的型別。 它必須設定為下列其中一個值： [總覽](#overview)、 [計量](#metrics)、 [CustomResources](#custom-resources)、 [關聯](#associations)。 如需詳細資訊，請參閱 [viewDefinition.js的目前 JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)。

View definition 的範例 JSON：

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

當您在 **viewDefinition.js**中提供此視圖時，它會覆寫受控應用程式中的預設 [總覽] 頁面。

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
|header|否|總覽頁面的標頭。|
|description|否|受控應用程式的描述。|
|命令|否|[總覽] 頁面的其他工具列按鈕陣列，請參閱 [命令](#commands)。|

![螢幕擷取畫面顯示具有測試動作控制項來執行示範應用程式之受控應用程式的總覽。](./media/view-definition/overview.png)

## <a name="metrics"></a>計量

`"kind": "Metrics"`

計量視圖可讓您從 [Azure 監視器計量](../../azure-monitor/platform/data-platform-metrics.md)中的受控應用程式資源收集並匯總資料。

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
|displayName|否|顯示之視圖的標題。|
|version|否|用來呈現視圖的平臺版本。|
|圖表|是|[計量] 頁面的圖表陣列。|

### <a name="chart"></a>圖表

|屬性|必要|描述|
|---------|---------|---------|
|displayName|是|顯示的圖表標題。|
|chartType|否|要用於此圖表的視覺效果。 依預設，它會使用折線圖。 支援的圖表類型： `Bar, Line, Area, Scatter` 。|
|metrics|是|要在此圖表上繪製的度量陣列。 若要深入瞭解 Azure 入口網站中支援的計量，請參閱 [支援的計量 Azure 監視器](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>計量

|屬性|必要|描述|
|---------|---------|---------|
|NAME|是|計量的名稱。|
|aggregationType|是|要用於此度量的匯總類型。 支援的匯總類型： `none, sum, min, max, avg, unique, percentile, count`|
|namespace|否|判斷正確的度量提供者時所使用的其他資訊。|
|resourceTagFilter|否|資源標記陣列 (將會以要 `or` 顯示度量的單字) 分隔。 適用于資源類型篩選準則的最上層。|
|resourceType|是|將顯示度量的資源類型。|

![螢幕擷取畫面顯示稱為 [我的計量視圖] 的 [監視] 頁面，其為受控應用程式。](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>自訂資源

`"kind": "CustomResources"`

您可以定義這種類型的多個視圖。 每個視圖都代表您在**mainTemplate.js**中定義的自訂提供者所提供的**唯一**自訂資源類型。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](../custom-providers/overview.md)\(英文\)。

在此視圖中，您可以針對您的自訂資源類型執行 GET、PUT、DELETE 和 POST 作業。 POST 作業可以是自訂資源類型內容中的全域自訂動作或自訂動作。

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
|displayName|是|顯示之視圖的標題。 您**viewDefinition.js**中的每個 CustomResources 視圖都必須有**唯一**的標題。|
|version|否|用來呈現視圖的平臺版本。|
|resourceType|是|自訂資源類型。 必須是自訂提供者的 **唯一** 自訂資源類型。|
|icon|否|視圖的圖示。 範例圖示清單是在 [JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定義。|
|createUIDefinition|否|建立用於建立自訂資源命令的 UI 定義架構。 如需建立 UI 定義的簡介，請參閱 [開始使用 CreateUiDefinition](create-uidefinition-overview.md)|
|命令|否|CustomResources 視圖的其他工具列按鈕陣列，請參閱 [命令](#commands)。|
|資料行|否|自訂資源之資料行的陣列。 如果未定義，則 `name` 預設會顯示資料行。 此資料行必須具有 `"key"` 和 `"displayName"` 。 在 [索引鍵] 中，提供要顯示在視圖中的屬性索引鍵。 如果是 nested，請使用點做為分隔符號，例如 `"key": "name"` 或 `"key": "properties.property1"` 。 針對 [顯示名稱]，提供要在視圖中顯示之屬性的顯示名稱。 您也可以提供 `"optional"` 屬性。 當設為 true 時，預設會在視圖中隱藏該資料行。|

![螢幕擷取畫面顯示稱為「測試自訂資源類型」和「控制項自訂內容動作」的資源頁面。](./media/view-definition/customresources.png)

## <a name="commands"></a>命令

命令是頁面上顯示之其他工具列按鈕的陣列。 每個命令都代表您在 **mainTemplate.js的**Azure 自訂提供者中定義的 POST 動作。 如需自訂提供者的簡介，請參閱 [Azure 自訂提供者總覽](../custom-providers/overview.md)。

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
|path|是|自訂提供者動作名稱。 動作必須在 **mainTemplate.js**中定義。|
|icon|否|命令按鈕的圖示。 範例圖示清單是在 [JSON 架構](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#)中定義。|
|createUIDefinition|否|建立命令的 UI 定義架構。 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。|

## <a name="associations"></a>關聯

`"kind": "Associations"`

您可以定義這種類型的多個視圖。 此視圖可讓您透過 **mainTemplate.js**中定義的自訂提供者，將現有資源連結至受控應用程式。 如需自訂提供者的簡介，請參閱 [Azure Custom Providers Preview 概觀](../custom-providers/overview.md)\(英文\)。

在此視圖中，您可以根據來擴充現有的 Azure 資源 `targetResourceType` 。 當您選取資源時，它會為 **公用** 自訂提供者建立上線要求，這可將副作用套用至資源。 

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
|displayName|是|顯示之視圖的標題。 您**viewDefinition.js**中的每個相關檢視都必須有**唯一**的標題。|
|version|否|用來呈現視圖的平臺版本。|
|targetResourceType|是|目標資源類型。 這是將為資源上線所顯示的資源類型。|
|createUIDefinition|否|建立建立關聯資源命令的 UI 定義架構。 如需建立 UI 定義的簡介，請參閱 [開始使用 CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>尋求協助

如果您對 Azure 受控應用程式有任何疑問，請嘗試在[堆疊溢位](https://stackoverflow.com/questions/tagged/azure-managedapps)提問。 類似的問題可能已有人詢問和回答，因此請先查看再張貼問題。 新增標記 `azure-managedapps` 以取得快速回應！

## <a name="next-steps"></a>後續步驟

- 如需受控應用程式的簡介，請參閱 [Azure 受控應用程式概觀](overview.md)。
- 如需自訂提供者的簡介，請參閱 [Azure 自訂提供者總覽](../custom-providers/overview.md)。
- 如需使用 Azure 自訂提供者建立 Azure 受控應用程式，請參閱 [教學課程：使用自訂提供者動作和資源類型來建立受控應用程式](tutorial-create-managed-app-with-custom-provider.md)
