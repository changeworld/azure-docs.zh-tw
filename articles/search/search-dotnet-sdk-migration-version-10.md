---
title: 升級至 Azure 認知搜尋 .NET SDK 第10版
titleSuffix: Azure Cognitive Search
description: 從舊版將程式碼遷移至 Azure 認知搜尋 .NET SDK 第10版。 了解新功能與必要的程式碼變更。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847512"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>升級至 Azure 認知搜尋 .NET SDK 第10版

如果您使用的是9.0 或更舊版本的[Azure 搜尋服務 .NET SDK](https://aka.ms/search-sdk)，本文將協助您將應用程式升級為使用第10版。

Azure 搜尋服務在第10版中重新命名為 Azure 認知搜尋，但命名空間和套件名稱不會變更。 舊版 SDK （9.0 和更早版本）會繼續使用先前的名稱。 如需使用 SDK 的詳細資訊（包括範例），請參閱[如何從 .Net 應用程式使用 Azure 認知搜尋](search-howto-dotnet-sdk.md)。

版本10新增了數個功能和 bug 修正，使其與最新版本的 REST API 版本`2019-05-06`具有相同的功能等級。 在變更中斷現有程式碼的情況下，我們將逐步引導您完成[解決問題所需的步驟](#UpgradeSteps)。

> [!NOTE]
> 如果您使用 8.0-preview 版或更舊版本，您應該先升級至第9版，然後再升級至版本10。 如需相關指示，請參閱[升級至 Azure 搜尋服務 .NET SDK 第9版](search-dotnet-sdk-migration-version-9.md)。
>
> 您的搜尋服務實例支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>第10版的新功能
Azure 認知搜尋的第10版 .NET SDK 以 REST API （`2019-05-06`）的最新正式運作版本為目標，其中包含下列更新：

* 引進兩種新的技能-[條件式技能](cognitive-search-skill-conditional.md)和[文字翻譯技能](cognitive-search-skill-text-translation.md)。
* 已重構的[整形人員技能](cognitive-search-skill-shaper.md)輸入，以容納來自嵌套內容的匯總。 如需詳細資訊，請參閱此[JSON 定義範例](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)。
* 加入兩個新的[欄位對應函數](search-indexer-field-mappings.md)：
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* 在某些情況下，在[索引子執行狀態](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)中顯示的錯誤和警告，可能會有其他詳細資料可協助您進行偵錯工具。 `IndexerExecutionResult`已更新以反映此行為。
* 您可以選擇性地指定[skillset](cognitive-search-defining-skillset.md) `name`屬性來識別技能集內定義的個別技能。
* `ServiceLimits`顯示[複雜類型](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)的限制， `IndexerExecutionInfo`並顯示相關的索引子限制/配額。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>升級步驟

1. 使用 NuGet 套件管理員主控台`Microsoft.Azure.Search`或以滑鼠右鍵按一下您的專案參考，然後選取 [管理 NuGet 套件 ...]，以更新您的 nuget 參考。在 Visual Studio。

2. 一旦 NuGet 下載新的封裝和其相依性，請重建您的專案。 

3. 如果您的組建失敗，您將需要修正每個組建錯誤。 如需如何解決每個可能的組建錯誤的詳細資訊，請參閱第[10 版的重大變更](#ListOfChanges)。

4. 在您修正所有建置錯誤或警告之後，就可以視需要對您的應用程式進行變更，以利用新的功能。 第[10 版的新](#WhatsNew)功能中詳述了 SDK 的新功能。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>第10版的重大變更

第10版有幾項重大變更，除了重建您的應用程式之外，可能還需要變更程式碼。

> [!NOTE]
> 以下的變更清單並不完整。 某些變更可能不會導致組建錯誤，但是在技術上會中斷，因為它們會破壞與舊版 Azure 認知搜尋 .NET SDK 元件相依的元件的二進位相容性。 在此類別下的重大變更也會與建議一併列出。 請在升級至第10版時重建您的應用程式，以避免發生二進位相容性問題。

### <a name="custom-web-api-skill-definition"></a>自訂 Web API 技能定義

[自訂 WEB API 技能](cognitive-search-custom-skill-web-api.md)的定義在9和更舊版本中不正確地指定。 

模型， `WebApiSkill`指定`HttpHeaders`為_包含_字典的物件屬性。 以這種方式建立`WebApiSkill`的技能集會導致例外狀況，因為 REST API 會將要求視為格式不正確。 已修正此問題，方法是在`HttpHeaders` `WebApiSkill`模型本身上建立**最上層字典屬性**-這會被視為來自 REST API 的有效要求。

例如，如果您之前嘗試具現化`WebApiSkill` ，如下所示：

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>整形的技能允許嵌套內容匯總

整形者技能現在可以允許來自嵌套內容的輸入匯總。 為了啟用這項變更，我們`InputFieldMappingEntry`已修改，讓它可以藉由只指定`Source`屬性或`SourceContext`和`Inputs`屬性來具現化。

您很可能不需要進行任何程式碼變更;但請注意，只允許這兩個組合的其中一個。 這表示：

- 建立`InputFieldMappingEntry` ，其中只有`Source`初始化的是有效的。
- 建立， `InputFieldMappingEntry`其中只有`SourceContext`和`Inputs`已初始化為有效。
- 與這三個屬性相關的其他所有組合都是不正確。

如果您決定要開始使用這項新功能，請先確定您的所有用戶端都已更新為使用第10版，再推出該變更。 否則，用戶端（使用較舊版本的 SDK）的更新可能會導致發生驗證錯誤。

> [!NOTE]
> 雖然基礎`InputFieldMappingEntry`模型已經過修改，以允許來自嵌套內容的匯總，但它的使用只在整形程式技能的定義內有效。 以其他技能使用這項功能，而在編譯時期有效，將會在執行時間產生驗證錯誤。

## <a name="skills-can-be-identified-by-a-name"></a>技能可以透過名稱來識別

技能集內的每個技能現在都有一個`Name`新的屬性，可以在您的程式碼中初始化以協助識別技能。 這是選擇性的-未指定時（這是預設值，如果未進行明確的程式碼變更），則會使用技能集中技能的以1為基礎的索引來指派預設名稱，並在前面加上 ' # ' 字元。 例如，在下列技能集定義中（為了簡潔起見，已略過大部分的初始化）：

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

`SentimentSkill`已指派名稱`#1`、 `WebApiSkill`指派`#2`、指派， `ShaperSkill` `#3`依此類推。

如果您選擇以自訂名稱來識別技能，請務必先將用戶端的所有實例更新為第10版的 SDK。 否則，使用舊版 SDK 的用戶端可能`null`會退出技能的`Name`屬性，而導致用戶端回到預設的命名配置。

## <a name="details-about-errors-and-warnings"></a>錯誤和警告的詳細資料

`ItemError`以及`ItemWarning`封裝在索引子執行期間所發生之錯誤和警告詳細資料的和模型，已修改為包含三個新的屬性，其目標是要協助進行索引子的偵錯工具。 這些屬性是：

- `Name`：產生錯誤之來源的名稱。 例如，它可以參考附加技能集中的特定技能。
- `Details`：錯誤或警告的其他詳細資訊。
- `DocumentationLink`：特定錯誤或警告的疑難排解指南連結。

> [!NOTE]
> 我們已開始結構我們的錯誤和警告，盡可能包含這些實用的詳細資料。 我們正努力確保所有的錯誤和警告都有這些詳細資料，但這是進行中的工作，而且不一定會擴展這些額外的詳細資料。

## <a name="next-steps"></a>後續步驟

- 對整形程式技能所做的變更，對於新的或現有的程式碼可能會有最大的影響。 在下一個步驟中，請務必重新流覽此範例，以說明輸入結構：[整形專家技能 JSON 定義範例](cognitive-search-skill-shaper.md)
- 請流覽[AI 擴充的總覽](cognitive-search-concept-intro.md)。
- 歡迎您提供 SDK 的意見反應。 如果您遇到問題，歡迎詢問我們[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)的協助。 如果您發現錯誤，您可以在 [Azure .NET SDK GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/issues)中提出問題。 請務必在您的問題標題前面加上「[Azure 認知搜尋]」。

