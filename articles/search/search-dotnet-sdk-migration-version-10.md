---
title: 升級到 Azure 認知搜索 .NET SDK 版本 10
titleSuffix: Azure Cognitive Search
description: 將代碼從舊版本遷移到 Azure 認知搜索 .NET SDK 版本 10。 了解新功能與必要的程式碼變更。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847512"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>升級到 Azure 認知搜索 .NET SDK 版本 10

如果您使用的是[Azure 搜索 .NET SDK](https://aka.ms/search-sdk)的版本 9.0 或更高級版本，本文將説明您將應用程式升級到使用版本 10。

Azure 搜索在版本 10 中重命名為 Azure 認知搜索，但命名空間和包名稱保持不變。 SDK 的早期版本（9.0 和更早版本）繼續使用前一個名稱。 有關使用 SDK 的詳細資訊（包括示例），請參閱[如何使用 .NET 應用程式中的 Azure 認知搜索](search-howto-dotnet-sdk.md)。

版本 10 添加了多個功能和錯誤修復，使其與 REST API 版本`2019-05-06`的最新版本具有相同的功能級別。 如果更改破壞了現有代碼，我們將引導您完成[解決問題所需的步驟](#UpgradeSteps)。

> [!NOTE]
> 如果您使用的是版本 8.0 預覽版或更版本的版本，則應首先升級到版本 9，然後升級到版本 10。 有關說明[，請參閱升級到 Azure 搜索 .NET SDK 版本 9。](search-dotnet-sdk-migration-version-9.md)
>
> 搜索服務實例支援多個 REST API 版本，包括最新的版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>版本 10 中的新增功能
Azure 認知搜索 .NET SDK 的版本 10 以最新的可用版本的 REST`2019-05-06`API （ ） 為目標，並具有以下更新：

* 引進兩種新技能-[條件技能和](cognitive-search-skill-conditional.md)[文本翻譯技能](cognitive-search-skill-text-translation.md)。
* [掃描器技能](cognitive-search-skill-shaper.md)輸入已進行了重組，以適應嵌套上下文中的整合。 有關詳細資訊，請參閱[此示例 JSON 定義](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)。
* 添加兩個新的[欄位映射函數](search-indexer-field-mappings.md)：
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* 在某些情況下，以[索引子執行狀態](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)顯示的錯誤和警告可以具有有助於調試的其他詳細資訊。 `IndexerExecutionResult`已更新以反映此行為。
* 可以通過指定`name`屬性來有選擇地識別[技能集中](cognitive-search-defining-skillset.md)中定義的單個技能。
* `ServiceLimits`顯示[複雜類型的](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)限制，並`IndexerExecutionInfo`顯示相關的索引子限制/配額。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟

1. 更新 NuGet 參考`Microsoft.Azure.Search`，以便使用 NuGet 包管理器主控台，或按右鍵專案參考並選擇"管理 NuGet 包..."在視覺工作室。

2. 一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 

3. 如果生成失敗，則需要修復每個建置錯誤。 有關如何解決每個潛在建置錯誤的詳細資訊，請參閱[版本 10 中的"中斷更改](#ListOfChanges)"。

4. 在您修正所有建置錯誤或警告之後，就可以視需要對您的應用程式進行變更，以利用新的功能。 SDK 中的新功能詳見版本[10 中的新增](#WhatsNew)功能。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>版本 10 中斷的更改

版本 10 中有幾個重大更改，除了重建應用程式之外，還需要更改代碼。

> [!NOTE]
> 下面的更改清單並非詳盡無遺。 某些更改可能不會導致建置錯誤，但在技術上會中斷，因為它們與依賴于早期版本的 Azure 認知搜索 .NET SDK 程式集的程式集中斷二進位相容性。 屬於此類別的重大更改也列出，並列出建議。 請重新生成應用程式時升級到版本 10，以避免任何二進位相容性問題。

### <a name="custom-web-api-skill-definition"></a>自訂 Web API 技能定義

自訂 Web [API 技能](cognitive-search-custom-skill-web-api.md)的定義在版本 9 和舊版中指定不正確。 

指定`WebApiSkill``HttpHeaders`為_包含_字典的物件屬性的模型。 以這種方式`WebApiSkill`構造創建技能集會導致異常，因為 REST API 會認為請求格式錯誤。 此問題已通過在`HttpHeaders``WebApiSkill`模型本身上創建**頂級字典屬性**（這被視為來自 REST API 的有效請求）得到了糾正。

例如，如果您以前嘗試具現化，`WebApiSkill`如下所示：

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

將其更改為以下內容，以避免 REST API 中的驗證錯誤：

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>沙珀技能允許嵌套上下文合併

Shaper 技能現在可以允許來自嵌套上下文的輸入整合。 為了啟用此更改，我們進行了`InputFieldMappingEntry`修改，以便只需指定`Source`屬性或 和`SourceContext``Inputs`屬性即可具現化。

您很可能不需要進行任何代碼更改;因此，您很可能不需要進行任何代碼更改。但是請注意，這兩種組合中只有一個允許。 這表示：

- 創建僅在`InputFieldMappingEntry``Source`初始化的一個是有效的。
- 創建一`InputFieldMappingEntry`個僅`SourceContext`初始`Inputs`化且正在初始化的項是有效的。
- 涉及這三個屬性的所有其他組合都無效。

如果您決定開始使用此新功能，請確保所有用戶端都更新為首先使用版本 10，然後再推出該更改。 否則，用戶端（使用舊版本的 SDK）對 Shaper 技能的更新可能會導致驗證錯誤。

> [!NOTE]
> 即使基礎`InputFieldMappingEntry`模型已修改，以允許從嵌套上下文中進行合併，但它的使用僅在 Shaper 技能的定義中有效。 在其他技能中使用此功能雖然在編譯時有效，但在運行時將導致驗證錯誤。

## <a name="skills-can-be-identified-by-a-name"></a>技能可以通過名稱識別

技能集中中的每個技能現在都有一個新屬性`Name`，可以在代碼中初始化，以説明識別技能。 這是可選的 - 當未指定（這是預設值，如果未進行顯式代碼更改），則使用技能集中技能的 1 個基於的索引為其分配預設名稱，該索引以"#"字元為預綴。 例如，在以下技能集定義中（為了簡潔起見，跳過了大多數初始化）：

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill``#1`分配的名稱，`WebApiSkill`分配`#2`，`ShaperSkill`分配`#3`等。

如果選擇按自訂名稱標識技能，請確保首先將用戶端的所有實例更新為 SDK 版本 10。 否則，使用舊版本的 SDK 的用戶端可能會`null`退出技能`Name`的屬性，從而導致用戶端依賴于預設命名方案。

## <a name="details-about-errors-and-warnings"></a>有關錯誤和警告的詳細資訊

`ItemError`對`ItemWarning`索引子執行期間發生的錯誤和警告（分別）的詳細資訊進行封裝的模型進行了修改，以包括三個新屬性，目的是説明調試索引子。 這些屬性是：

- `Name`： 錯誤源自的源的名稱。 例如，它可以引用附加技能集中的特定技能。
- `Details`：有關錯誤或警告的其他詳細詳細資訊。
- `DocumentationLink`：指向特定錯誤或警告的故障排除指南的連結。

> [!NOTE]
> 我們已經開始構造錯誤和警告，以便盡可能包含這些有用的詳細資訊。 我們正在努力確保對於所有錯誤和警告，這些詳細資訊都存在，但這是一項正在進行中的工作，並且這些附加詳細資訊可能並不總是被填充。

## <a name="next-steps"></a>後續步驟

- 對 Shaper 技能的更改對新代碼或現有代碼具有最大的潛在影響。 作為下一步，請務必重新訪問此示例，說明輸入結構：[沙珀技能 JSON 定義示例](cognitive-search-skill-shaper.md)
- 流覽[AI 擴充概述](cognitive-search-concept-intro.md)。
- 歡迎您提供 SDK 的意見反應。 如果您遇到問題，請隨時向我們尋求説明[，在堆疊溢位](https://stackoverflow.com/questions/tagged/azure-search)。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請確保使用"[Azure 認知搜索]"為問題標題加綴。

