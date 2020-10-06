---
title: API 版本
titleSuffix: Azure Cognitive Search
description: 適用于 .NET SDK 中 Azure 認知搜尋 REST Api 和用戶端程式庫的版本原則。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 71862bedc009d560adc8131eacc37c0afba25d81
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761714"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 認知搜尋中的 API 版本

Azure 認知搜尋定期推出功能更新。 有時候 (但並不一定)，這些更新會需要新版本的 API，以維持回溯相容性。 發行新版本可讓您控制在程式碼中整合搜尋服務更新的時機與方式。

根據規則，Azure 認知搜尋小組只會在必要時發佈新版本，因為這可能會需要一些精力來將您的程式碼升級，以使用新的 API 版本。 只有在以破壞回溯相容性的方式變更 API 的某些層面時，才需要新版本。 在修正現有的功能，或新功能會變更現有 API 的介面區時，就會發生這類變更。

相同的規則適用於 SDK 更新。 Azure 認知搜尋 SDK 會遵循 [語義版本控制](https://semver.org/) 規則，這表示其版本有三個部分：主要、次要和組建編號 (例如，1.1.0) 。 只有在變更會破壞回溯相容性時，才會發行新的主要版本 SDK。 非重大功能更新會遞增次要版本，而錯誤修正只會遞增組建版本。

> [!Important]
> 適用于 .NET、JAVA、Python 和 JavaScript 的 Azure Sdk 即將推出 Azure 認知搜尋的新用戶端程式庫。 目前，沒有任何 Azure SDK 程式庫完全支援最新的搜尋 REST Api (2020-06-30) 或管理 REST Api (2020-03-13) 但這會隨著時間而改變。 您可以定期檢查此頁面或功能增強功能的 [新](whats-new.md) 功能。

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>不支援的版本

2020年10月15日之前，將現有的搜尋解決方案升級為最新版的 REST API。 屆時，將會淘汰下列版本的 Azure 認知搜尋 REST API，且不再支援：

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

此外，Azure 認知搜尋版 [**3.0.0**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) 的 .net SDK 版本也將會被淘汰，因為這些版本是以其中一個 REST API 版本為目標。 

在此日期之後，使用任何已淘汰 REST API 或 SDK 版本的應用程式將無法再運作，而且必須進行升級。 就像這種類型的任何變更一樣，我們會提供12個月的通知，讓您有足夠的時間進行調整。

若要繼續使用 Azure 認知搜尋，請在2020年10月15日之前，將以 [REST API](search-api-migration.md) 為目標的現有程式碼遷移至 [REST API 2020-06-30 版](https://docs.microsoft.com/rest/api/searchservice/) 或更新版本的 SDK。  如果您有任何關於更新至最新版本的問題，請傳送電子郵件至 azuresearch_contact@microsoft.com 2020 5 月15日，以確保您有足夠的時間更新您的程式碼。

## <a name="rest-apis"></a>REST API

Azure 認知搜尋服務實例支援數個 REST API 版本，包括最新版本。 當版本不再是最新版本時，您可以繼續使用該版本，但建議您 [遷移程式碼](search-api-migration.md) 以使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

下表提供搜尋服務 REST API 的目前版本和先前發行版本的版本歷程記錄。 檔僅針對最新的穩定版本和預覽版本發行。

### <a name="search-service-apis"></a>搜尋服務 Api

在搜尋服務上建立和管理內容。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 狀態 | 描述 |
|-------------------------|--------|------------------------------|
| [搜尋 2020-06-30](/rest/api/searchservice/index)| 穩定 | 最新穩定版本的搜尋 REST Api，並在相關性評分和正式推出的知識存放區中提供進展。|
| [搜尋 2020-06-30-預覽](/rest/api/searchservice/index-preview)| 預覽 | 與穩定版本相關聯的預覽版本。 包含多項 [預覽功能](search-api-preview.md)。 |
| 搜尋 2019-05-06 | 穩定  | 加入 [複雜類型](search-howto-complex-data-types.md)。 |
| 搜尋 2019-05-06-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋2017-11-11 | 穩定 | 新增技能集和 [AI 擴充](cognitive-search-concept-intro.md)。 |
| 搜尋 2017-11-11-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋2016-09-01 |穩定 | 加入 [索引子](search-indexer-overview.md)。 |
| 搜尋 2016-09-01-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。|
| 搜尋2015-02-28 | 10-10-2020 之後不支援   | 第一個正式推出的版本。  |
| 搜尋 2015-02-28-預覽 | 10-10-2020 之後不支援  | 與穩定版本相關聯的預覽版本。 |
| 搜尋 2014-10-20-預覽 | 10-10-2020 之後不支援 | 第二個公開預覽版。 |
| 搜尋 2014-07-31-預覽 | 10-10-2020 之後不支援  | 第一個公開預覽。 |

### <a name="management-rest-apis"></a>管理 REST API

建立並設定搜尋服務，以及管理 API 金鑰。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 狀態 | 描述 |
|-------------------------|--------|------------------------------|
| [管理2020-08-01](https://docs.microsoft.com/rest/api/searchmanagement/) | 穩定 | 管理 REST Api 的最新穩定版本。 新增所有輸出存取資源的正式可用共用私人連結資源支援，但預覽版本中注明的資源除外 |
| [管理 2020-08-01-預覽](https://docs.microsoft.com/rest/api/searchmanagement/index-preview) | 預覽  | 目前處於預覽狀態： Azure Functions 和適用於 MySQL 的 Azure 資料庫的共用私人連結資源支援。 |
| 管理 2020-03-13  | 穩定 | 透過私人連結新增 [私用端點](service-create-private-endpoint.md) ，以及新服務的 [網路 IP 規則](service-configure-firewall.md) 。 如需詳細資訊，請參閱此 [swagger 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01)。 |
| 管理 2019-10-01-預覽 | 預覽  | 這份清單中沒有引入任何預覽功能。 此預覽功能相當於2020-03-13。 如需詳細資訊，請參閱此 [swagger 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview)。 |
| 管理2015-08-19  | 穩定 | 第一個正式推出的管理 REST Api 版本。 提供服務提供、擴大和 api 金鑰管理。 如需詳細資訊，請參閱此 [swagger 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19)。 |
| 管理 2015-08-19-預覽  | 預覽 | 管理 REST Api 的第一個預覽版本。 如需詳細資訊，請參閱此 [swagger 規格](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19)。 |

## <a name="azure-sdk-for-net"></a>適用於 .NET 的 Azure SDK

下表提供較新 SDK 版本的連結。 

| SDK 版本 | 狀態 | 描述 |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11。0](/dotnet/api/overview/azure/search.documents-readme) | 穩定 | 從2020年7月發行的 Azure .NET SDK 新用戶端程式庫。 以搜尋 REST api 版本 = 2020-06-30 REST API 為目標，但尚未支援、地理篩選或 [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder)。 |
| [Microsoft Azure. 搜尋10。0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | 穩定 | 發行日期可能是2019。 以搜尋 REST api 版本 = 2019-05-06 為目標。|
| [Microsoft. 管理。搜尋4.0。0](/dotnet/api/overview/azure/search/management) | 穩定 | 以管理 REST api 版本 = 2020-08-01 為目標。  |
| Microsoft. 管理。搜尋3.0。0 | 穩定 | 以管理 REST api 版本 = 2015-08-19 為目標。  |

## <a name="azure-sdk-for-java"></a>Azure SDK for Java

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [JAVA azure-搜尋-檔11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | 穩定 | 從2020年7月發行的 Azure .NET SDK 新用戶端程式庫。 以搜尋 REST api 版本 = 2019-05-06 為目標。 |
| [JAVA 管理用戶端1.35。0](/java/api/overview/azure/search/management) | 穩定 | 以管理 REST api 版本 = 2015-08-19 為目標。 |

## <a name="azure-sdk-for-javascript"></a>適用於 JavaScript 的 Azure SDK

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [JavaScript azure-搜尋11。0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | 穩定 | 從2020年7月發行的 Azure .NET SDK 新用戶端程式庫。 以搜尋 REST api 版本 = 2016-09-01 為目標。 |
| [JavaScript azure-arm-搜尋](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | 穩定 | 以管理 REST api 版本 = 2015-08-19 為目標。 |

## <a name="azure-sdk-for-python"></a>適用於 Python 的 Azure SDK

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [Python azure-搜尋-檔11。0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | 穩定 | 從2020年7月發行的 Azure .NET SDK 新用戶端程式庫。 以搜尋 REST api 版本 = 2019-05-06 為目標。 |
| [Python azure-管理-搜尋1。0](/python/api/overview/azure/search) | 穩定 | 以管理 REST api 版本 = 2015-08-19 為目標。 |