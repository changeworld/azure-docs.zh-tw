---
title: API 版本
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋 REST Api 的版本原則和 .NET SDK 中的用戶端程式庫。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 5be50453dff9acaf4a9876eec1d95b56abebf745
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029836"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 認知搜尋中的 API 版本

Azure 認知搜尋會定期推出功能更新。 有時候 (但並不一定)，這些更新會需要新版本的 API，以維持回溯相容性。 發行新版本可讓您控制在程式碼中整合搜尋服務更新的時機與方式。

根據規則，Azure 認知搜尋小組只會在必要時發佈新版本，因為它可能需要一些時間來升級您的程式碼，以使用新的 API 版本。 只有在以破壞回溯相容性的方式變更 API 的某些層面時，才需要新版本。 在修正現有的功能，或新功能會變更現有 API 的介面區時，就會發生這類變更。

相同的規則適用於 SDK 更新。 Azure 認知搜尋 SDK 會遵循[語義版本控制](https://semver.org/)規則，這表示其版本有三個部分：主要、次要和組建編號（例如，1.1.0）。 只有在變更會破壞回溯相容性時，才會發行新的主要版本 SDK。 非重大功能更新會遞增次要版本，而錯誤修正只會遞增組建版本。

> [!Important]
> 適用于 .NET、JAVA、Python 和 JavaScript 的 Azure Sdk 會推出新的用戶端程式庫，以進行 Azure 認知搜尋。 目前，沒有任何 Azure SDK 程式庫完全支援最新的搜尋 REST Api （2020-06-30）或管理 REST Api （2020-03-13），但這會隨著時間而改變。 您可以定期查看此頁面，或在功能增強功能上發表通知的[新](whats-new.md)功能。 

## <a name="rest-apis"></a>REST API

Azure 認知搜尋服務實例支援數個 REST API 版本，包括最新版本。 當不再是最新的版本時，您可以繼續使用它，但我們建議您將[程式碼遷移](search-api-migration.md)為使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

下表提供目前和先前發行版本本的搜尋服務 REST API 的版本歷程記錄。 檔僅針對最新的穩定和預覽版本發佈。

### <a name="search-service-apis"></a>搜尋服務 Api

在搜尋服務上建立和管理內容。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 狀態 | 回溯相容性問題 |
|-------------------------|--------|------------------------------|
| [搜尋 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| 穩定 | 最新穩定版本的搜尋 REST Api，並在相關性計分和已正式推出的知識存放區中獲得進展。|
| [搜尋 2020-06-30-預覽](https://docs.microsoft.com/rest/api/searchservice/index-preview)| 預覽 | 與穩定版本相關聯的預覽版本。 包含多項[預覽功能](search-api-preview.md)。 |
| 搜尋 2019-05-06 | 穩定 | 加入[複雜類型](search-howto-complex-data-types.md)。 |
| 搜尋 2019-05-06-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋2017-11-11 | 穩定  | 新增技能集和[AI 擴充](cognitive-search-concept-intro.md)。 |
| 搜尋 2017-11-11-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋2016-09-01 |穩定 | 加入[索引子](search-indexer-overview.md)。 |
| 搜尋 2016-09-01-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。|
| 搜尋2015-02-28 | 穩定  | 第一個正式推出版本。  |
| 搜尋 2015-02-28-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋 2014-10-20-預覽 | 預覽 | 第二個公開預覽。 |
| 搜尋 2014-07-31-預覽 | 預覽 | 第一個公開預覽。 |

### <a name="management-rest-apis"></a>管理 REST API

建立和設定搜尋服務，以及管理 API 金鑰。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 狀態 | 回溯相容性問題 |
|-------------------------|--------|------------------------------|
| [管理 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | 穩定 | 管理 REST Api 的最新穩定版本，在端點保護方面有進展。 透過私人連結和新服務的[網路 IP 規則](service-configure-firewall.md)新增[私人端點](service-create-private-endpoint.md)。 |
| [管理 2019-10-01-預覽](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | 預覽  | 雖然這是其版本號碼，但仍是管理 REST Api 的目前預覽版本。 目前沒有任何預覽功能。 所有預覽功能最近都已轉換為正式推出。 |
| 管理2015-08-19  | 穩定 | 管理 REST Api 的第一個正式推出版本。 提供服務布建、相應增加和 api 金鑰管理。 |
| 管理 2015-08-19-預覽 | 預覽 | 管理 REST Api 的第一個預覽版本。 |

## <a name="azure-sdk-for-net"></a>適用於 .NET 的 Azure SDK

套件版本歷程記錄可在 NuGet.org 上取得。下表提供每個封裝頁面的連結。


| SDK 版本 | 狀態 | 描述 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11。0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | 穩定 | Azure .NET SDK 的新用戶端程式庫已于2020年7月發行。 以搜尋 REST api 版本 = 2020-06-30 REST API 為目標，但尚不支援、異地篩選或[FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet)。 |
| [Microsoft. Azure 搜尋10。0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | 穩定 | 發行日期可能是2019。 以搜尋 REST api 版本 = 2019-05-06 作為目標。|
| [Microsoft Azure 搜尋 8.0-preview](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | 預覽 | 2019年4月發行。 以搜尋 REST api 版本 = 2019-05 06-01.5.1-Preview 作為目標。|
| [3.0.0 的搜尋](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。  |

## <a name="azure-sdk-for-java"></a>Azure SDK for Java

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [JAVA azure-搜尋-檔11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | 穩定 | Azure .NET SDK 的新用戶端程式庫已于2020年7月發行。 以搜尋 REST api 版本 = 2019-05-06 作為目標。 |
| [JAVA 管理用戶端1.35。0](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。 |

## <a name="azure-sdk-for-javascript"></a>適用於 JavaScript 的 Azure SDK

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [JavaScript azure-搜尋11。0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | 穩定 | Azure .NET SDK 的新用戶端程式庫已于2020年7月發行。 將目標設為搜尋 REST api-版本 = 2016-09-01。 |
| [JavaScript azure-arm-搜尋](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。 |

## <a name="azure-sdk-for-python"></a>適用於 Python 的 Azure SDK

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [Python azure-搜尋-檔11。0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | 穩定 | Azure .NET SDK 的新用戶端程式庫已于2020年7月發行。 以搜尋 REST api 版本 = 2019-05-06 作為目標。 |
| [Python azure-管理-搜尋1。0](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。 |