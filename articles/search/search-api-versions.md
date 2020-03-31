---
title: .NET 和 REST 的 API 版本管理
titleSuffix: Azure Cognitive Search
description: Azure 認知搜索 REST API 和 .NET SDK 中的用戶端庫的版本原則。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137283"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 認知搜索中的 API 版本

Azure 認知搜索定期發佈功能更新。 有時候 (但並不一定)，這些更新會需要新版本的 API，以維持回溯相容性。 發行新版本可讓您控制在程式碼中整合搜尋服務更新的時機與方式。

通常，Azure 認知搜索團隊僅在必要時發佈新版本，因為它可能需要一些工作來升級代碼以使用新的 API 版本。 只有在以破壞回溯相容性的方式變更 API 的某些層面時，才需要新版本。 在修正現有的功能，或新功能會變更現有 API 的介面區時，就會發生這類變更。

相同的規則適用於 SDK 更新。 Azure 認知搜索 SDK 遵循[語義版本控制](https://semver.org/)規則，這意味著其版本有三個部分：主要版本、次要版本號和生成編號（例如 1.1.0）。 只有在變更會破壞回溯相容性時，才會發行新的主要版本 SDK。 非重大功能更新會遞增次要版本，而錯誤修正只會遞增組建版本。

> [!NOTE]
> Azure 認知搜索服務實例支援多個 REST API 版本，包括最新的版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

## <a name="snapshot-of-current-versions"></a>目前版本的快照
下面是 Azure 認知搜索所有程式設計介面的當前版本的快照。


| 介面 | 最新主要版本 | 狀態 |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |一般提供，2019 年 5 月發佈 |
| [.NET SDK 預覽版](https://aka.ms/search-sdk-preview) |8.0 預覽版 |預覽版，2019 年 4 月發佈 |
| [服務 REST API](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |正式推出 |
| [服務 REST API 2019-05-06-預覽](search-api-preview.md) |2019-05-06-預覽 |預覽 |
| [.NET 管理 SDK](https://aka.ms/search-mgmt-sdk) |3.0 |正式推出 |
| [管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|正式推出 |

針對 REST API，必須在每個呼叫中納入 `api-version` 。 使用 `api-version` 會使將特定版本 (例如預覽 API) 作為目標較為容易。 下列範例說明如何指定 `api-version` 參數：

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> 雖然每個要求都具有 `api-version`，我們仍建議您針對所有的 API 要求使用相同的版本。 特別是當新的 API 版本引入舊版本無法辨識的屬性或作業時更建議您這樣做。 混合的 API 版本可能會有非預期的結果，應該予以避免。
>
> 服務 REST API 和管理 REST API 的版本是彼此獨立的。 版本號碼如有類似，純屬巧合。

正式推出 (或 GA) 的 API 可以用於實際執行，並且受到 Azure 服務等級協定所約束。 預覽版本具有實驗性功能，那些功能並不會都移轉到 GA 版本上。 **強烈建議您避免在實際執行的應用程式中使用預覽 API。**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>在 2020 年 10 月 15 日前更新到最新版本的 REST API
以下版本的 Azure 認知搜索 REST API 將從 2020 年 10 月 15 日停用，不再受支援 **：2014-07-31-預覽**版 **、2014-10-20 預覽**版 **、2015-02-28 預覽**版和**2015-02-28**。 此外，早于**3.0.0-rc**的 Azure 認知搜索 .NET SDK 版本也將停用，因為它們的目標是這些 REST API 版本之一。 在此日期之後，使用任何已棄用的 REST API 或 SDK 版本的應用程式將不再工作，必須升級。 與此類的任何更改一樣，我們提前 12 個月通知，因此您有足夠的時間進行調整。  要繼續使用 Azure 認知搜索，請在 2020 年 10 月 15 日前將目標[REST API](search-api-migration.md)的現有代碼遷移到 REST API[版本 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/)或更新版本或 .NET SDK 到[版本 3.0](search-dotnet-sdk-migration.md)或更新。  如果您對更新到最新版本有任何疑問，請在 2020 年azuresearch_contact@microsoft.com5 月 15 日前發送郵件，以確保有足夠的時間更新代碼。

## <a name="about-preview-and-generally-available-versions"></a>關於預覽與正式推出版本
Azure 認知搜索始終首先通過 REST API 預發佈實驗功能，然後通過 .NET SDK 的預發佈版本預發佈。

預覽功能適用於測試和實驗，目標為收集有關功能設計和實作的意見反應。 基於這個原因，預覽功能可能會隨時間變更，很可能以中斷回溯相容性的方式進行。 這與 GA 版本中的功能形成鮮明對比，除了小型的回溯相容性修正和增強功能外，這些功能很穩定，不太可能變更。 此外，預覽功能不一定最終都會納入 GA 版本。

基於這些原因，建議您避免撰寫依存於預覽版本的實際程式碼。 如果您正在使用舊版預覽版本，建議您移轉到正式推出的 (GA) 版本。

如需程式碼移轉的 .NET SDK：指南，請參閱 [升級 .NET SDK](search-dotnet-sdk-migration-version-9.md)。

通用可用性意味著 Azure 認知搜索現在處於服務等級協定 （SLA） 之下。 SLA 可以在 Azure[認知搜索服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)中找到。
