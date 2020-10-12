---
title: 升級至 .NET SDK 第10版
titleSuffix: Azure Cognitive Search
description: 將程式碼遷移至舊版的 Azure 認知搜尋 .NET SDK 版本10。 了解新功能與必要的程式碼變更。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bfe24ff38446fa0d0ccea96799e6f42b561713bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89002805"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>升級至 Azure 認知搜尋 .NET SDK 版本10

如果您使用的是9.0 版或更舊版本的 [.NET SDK](/dotnet/api/overview/azure/search)，本文將協助您升級應用程式以使用第10版。

Azure 搜尋服務會重新命名為10版中的 Azure 認知搜尋，但命名空間和套件名稱不會變更。 舊版 SDK (9.0 及更早版本) 繼續使用先前的名稱。 如需使用 SDK 的詳細資訊（包括範例），請參閱 [如何從 .Net 應用程式使用 Azure 認知搜尋](search-howto-dotnet-sdk.md)。

第10版新增了幾項功能和 bug 修正，使其與 REST API 版本的功能等級相同 `2019-05-06` 。 在變更中斷現有程式碼的情況下，我們將逐步引導您完成 [解決問題所需的步驟](#UpgradeSteps)。

> [!NOTE]
> 如果您使用的是 8.0-preview 版或更舊版本，您應該先升級至第9版，然後再升級至第10版。 如需相關指示，請參閱 [升級至 Azure 搜尋服務 .NET SDK 第9版](search-dotnet-sdk-migration-version-9.md) 。
>
> 您的搜尋服務實例支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>第10版的新功能
第10版的 Azure 認知搜尋 .NET SDK 的目標 REST API `2019-05-06` 有這些更新：

* 引進兩個新的技能- [條件式技能](cognitive-search-skill-conditional.md) 和 [文字翻譯技能](cognitive-search-skill-text-translation.md)。
* 重設[整形技巧](cognitive-search-skill-shaper.md)輸入，以容納來自嵌套內容的匯總。 如需詳細資訊，請參閱此 [範例 JSON 定義](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts)。
* 新增兩個新的 [欄位對應函數](search-indexer-field-mappings.md)：
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* 在某些情況下，在 [索引子執行狀態](/rest/api/searchservice/get-indexer-status) 中顯示的錯誤和警告可能會有其他詳細資料可協助進行偵錯工具。 `IndexerExecutionResult` 已更新以反映此行為。
* 您可以藉由指定屬性，選擇性地識別 [技能集](cognitive-search-defining-skillset.md) 中定義的個別技能 `name` 。
* `ServiceLimits` 顯示 [複雜類型](./search-howto-complex-data-types.md) 的限制，並 `IndexerExecutionInfo` 顯示相關的索引子限制/配額。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟

1. `Microsoft.Azure.Search`使用 nuget 封裝管理員主控台或以滑鼠右鍵按一下您的專案參考，然後選取 [管理 NuGet 套件 ...]，以更新您的 nuget 參考。在 Visual Studio。

2. 一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 

3. 如果您的組建失敗，您將需要修正每個組建錯誤。 如需如何解決每個可能組建錯誤的詳細資訊，請參閱第 [10 版的重大變更](#ListOfChanges) 。

4. 在您修正所有建置錯誤或警告之後，就可以視需要對您的應用程式進行變更，以利用新的功能。 第 [10 版的新](#WhatsNew)功能會詳述 SDK 中的新功能。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>第10版的重大變更

第10版中有幾項重大變更，除了重建您的應用程式之外，可能還需要變更程式碼。

> [!NOTE]
> 下列變更清單不詳盡。 某些變更可能不會造成組建錯誤，但在技術上會中斷，因為它們會中斷與相依于舊版 Azure 認知搜尋 .NET SDK 元件的元件的二進位相容性。 此類別下的重大變更也會與建議一併列出。 請在升級至版本10時重建您的應用程式，以避免任何二進位相容性問題。

### <a name="custom-web-api-skill-definition"></a>自訂 Web API 技能定義

[自訂 WEB API 技能](cognitive-search-custom-skill-web-api.md)的定義在9版及更舊版本中不正確地指定。 

`WebApiSkill`指定 `HttpHeaders` 為_包含_字典之物件屬性的模型。 以 `WebApiSkill` 這種方式建立的技能集會導致例外狀況，因為 REST API 會將要求視為格式不正確。 已更正此問題，方法是 `HttpHeaders` 在模型本身上建立 **最上層的字典屬性** `WebApiSkill` -這會被視為來自 REST API 的有效要求。

例如，如果您先前嘗試以具現化，如下所示 `WebApiSkill` ：

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

將它變更為下列內容，以避免來自 REST API 的驗證錯誤：

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>整形技能允許嵌套的內容匯總

整形者技能現在可允許來自嵌套內容的輸入匯總。 為了啟用這項變更，我們修改了，只要 `InputFieldMappingEntry` 指定 `Source` 屬性，或是同時指定和屬性，就可以將它具現化 `SourceContext` `Inputs` 。

您很可能不需要進行任何程式碼變更;不過請注意，只允許這兩種組合的其中一個。 這表示：

- 建立 `InputFieldMappingEntry` 只有初始化的會 `Source` 是有效的。
- 建立 `InputFieldMappingEntry` 只有 `SourceContext` 和 `Inputs` 的初始化是有效的。
- 牽涉到這三個屬性的所有其他組合都無效。

如果您決定開始使用這項新功能，請先確定您的所有用戶端都已更新為使用第10版，再推出該變更。 否則，用戶端可能會 (使用較舊版本的 SDK) 來進行更新，因此可能會導致驗證錯誤。

> [!NOTE]
> 雖然基礎 `InputFieldMappingEntry` 模型已經過修改，以允許從嵌套內容進行匯總，但它的使用只在整形程式技能的定義中有效。 在編譯時期使用這項功能，在編譯時期有效，將會在執行時間產生驗證錯誤。

## <a name="skills-can-be-identified-by-a-name"></a>技能可依名稱識別

技能集中的每個技能現在都有新的屬性 `Name` ，可以在您的程式碼中初始化以協助識別技能。 這是選擇性的-如果未指定 (預設值，如果沒有明確) 變更程式碼，則會使用技能集中的技能以1為基礎的索引，並在前面加上 ' # ' 字元來指派預設名稱。 例如，在下列技能集定義中 (為了簡潔起見，已略過大部分的初始化) ：

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

`SentimentSkill` 指派了名稱、指派給 `#1` `WebApiSkill` 、指派 `#2` ， `ShaperSkill` `#3` 依此類推。

如果您選擇以自訂名稱識別技能，請務必先將用戶端的所有實例更新為第10版的 SDK。 否則，使用較舊版本 SDK 的用戶端可能會無法使用 `null` `Name` 技能的屬性，而導致用戶端回到預設的命名配置。

## <a name="details-about-errors-and-warnings"></a>錯誤和警告的詳細資料

`ItemError` 以及將 `ItemWarning` 錯誤和警告詳細資料分別封裝 (在索引子執行期間發生) 的模型，已修改為包含三個新的屬性，目標是要協助偵測索引子。 這些屬性是：

- `Name`：產生錯誤之來源的名稱。 例如，它可以參考附加技能集中的特定技能。
- `Details`：有關錯誤或警告的其他詳細資訊。
- `DocumentationLink`：特定錯誤或警告的疑難排解指南連結。

> [!NOTE]
> 我們已開始結構錯誤和警告，盡可能包含這些有用的詳細資料。 我們正致力於確保所有錯誤和警告都存在這些詳細資料，但這是進行中的工作，而且不一定會填入這些額外的詳細資料。

## <a name="next-steps"></a>後續步驟

- 對整形程式技能的變更對於新的或現有的程式碼可能會有最大的影響。 在下一個步驟中，請務必重新流覽此範例以說明輸入結構： [整形技能 JSON 定義範例](cognitive-search-skill-shaper.md)
- 前往 [AI 擴充總覽](cognitive-search-concept-intro.md)。
- 歡迎您提供 SDK 的意見反應。 如果您遇到問題，歡迎您尋求 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)的協助。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請務必在問題標題前面加上 "[Azure 認知搜尋]"。