---
title: API 版本
titleSuffix: Azure Cognitive Search
description: Azure 認知搜尋 REST Api 的版本原則和 .NET SDK 中的用戶端程式庫。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830089"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Azure 認知搜尋中的 API 版本

Azure 認知搜尋會定期推出功能更新。 有時候 (但並不一定)，這些更新會需要新版本的 API，以維持回溯相容性。 發行新版本可讓您控制在程式碼中整合搜尋服務更新的時機與方式。

根據規則，Azure 認知搜尋小組只會在必要時發佈新版本，因為它可能需要一些時間來升級您的程式碼，以使用新的 API 版本。 只有在以破壞回溯相容性的方式變更 API 的某些層面時，才需要新版本。 在修正現有的功能，或新功能會變更現有 API 的介面區時，就會發生這類變更。

相同的規則適用於 SDK 更新。 Azure 認知搜尋 SDK 會遵循[語義版本控制](https://semver.org/)規則，這表示其版本有三個部分：主要、次要和組建編號（例如，1.1.0）。 只有在變更會破壞回溯相容性時，才會發行新的主要版本 SDK。 非重大功能更新會遞增次要版本，而錯誤修正只會遞增組建版本。

> [!NOTE]
> Azure 認知搜尋服務實例支援數個 REST API 版本，包括最新版本。 當一個版本不再是最新版本時，您仍可繼續使用該版本，但建議您將程式碼移轉成使用最新版本。 使用 REST API 時，您必須每個要求中透過 api-version 參數指定 API 版本。 使用 .NET SDK 時，您使用的 SDK 版本會決定對應的 REST API 版本。 如果您使用的是舊版 SDK，則即使服務已升級成支援新版 API 版本，您仍可繼續執行該程式碼而無須變更。

## <a name="rest-apis"></a>REST API

下表提供目前和先前發行的搜尋服務 REST API 版本歷程記錄。 檔已發行，適用于目前的穩定和預覽版本。

| 版本&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | 狀態 | 回溯相容性問題 |
|-------------|--------|------------------------------|
| [管理 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | 正式推出 | 管理 REST Api 的最新穩定版本，在端點保護方面有進展。 新增新服務的私用端點、私用連結支援和網路規則。 |
| [管理 2019-10-01-預覽](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | 預覽  | 雖然這是其版本號碼，但仍是管理 REST Api 的目前預覽版本。 目前沒有任何預覽功能。 所有預覽功能最近都已轉換為正式推出。 |
| 管理2015-08-19  | 穩定| 管理 REST Api 的第一個正式推出版本。 提供服務布建、相應增加和 api 金鑰管理。 |
| 管理 2015-08-19-預覽 | 預覽| 管理 REST Api 的第一個預覽版本。 |
| [搜尋 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| 穩定 | 最新穩定版本的搜尋搜尋 REST Api，並在相關性評分方面有進展。 |
| [搜尋 2020-06-30-預覽](https://docs.microsoft.com/rest/api/searchservice/index-preview)| 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋 2019-05-06 | 穩定 | 加入複雜類型。 |
| 搜尋 2019-05-06-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋2017-11-11 | 穩定  | 新增技能集和 AI 擴充。 |
| 搜尋 2017-11-11-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋2016-09-01 |穩定 | 加入索引子|
| 搜尋 2016-09-01-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。|
| 搜尋2015-02-28 | 穩定  | 第一個正式推出版本。  |
| 搜尋 2015-02-28-預覽 | 預覽 | 與穩定版本相關聯的預覽版本。 |
| 搜尋 2014-10-20-預覽 | 預覽 | 第二個公開預覽。 |
| 搜尋 2014-07-31-預覽 | 預覽 | 第一個公開預覽。 |

## <a name="azure-sdk-for-net"></a>適用於 .NET 的 Azure SDK

套件版本歷程記錄可在 NuGet.org 上取得。下表提供每個封裝頁面的連結。

| SDK 版本 | 狀態 | 描述 |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0-preview 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | 預覽 | 來自 Azure .NET SDK 的新用戶端程式庫已于2020年5月發行。 以 REST 2020-06-30 API 版本為目標|
| [**Microsoft. Azure 搜尋10。0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | 正式推出，2019年5月發行。 以 REST 2019-05-06 API 版本為目標。|
| [**Microsoft Azure 搜尋 8.0-preview**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | 預覽，2019年4月發行。 以 REST 2019-05-06-Preview API 版本為目標。|
| [**3.0.0 的搜尋**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。 |

## <a name="azure-sdk-for-java"></a>Azure SDK for Java

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [**JAVA SearchManagementClient 1.35。0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。|

## <a name="azure-sdk-for-python"></a>適用於 Python 的 Azure SDK

| SDK 版本 | 狀態 | 描述  |
|-------------|--------|------------------------------|
| [**Python azure-管理-搜尋1。0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | 穩定 | 以管理 REST api-版本 = 2015-08-19 作為目標。 |