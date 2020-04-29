---
title: '使用 c # 程式碼從 GA 環境查詢資料-Azure 時間序列深入解析 |Microsoft Docs'
description: '瞭解如何使用以 c # 撰寫的自訂應用程式來查詢 Azure 時間序列深入解析環境中的資料。'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: 754d1b80236d138693987cccee7a218ccd96b16b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383877"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>使用 C 從 Azure 時間序列深入解析 GA 環境查詢資料#

此 c # 範例示範如何使用[Ga 查詢 api](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)來查詢 Azure 時間序列深入解析 ga 環境中的資料。

> [!TIP]
> 查看 GA c # 程式碼[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)範例，網址為。

## <a name="summary"></a>[摘要]

下面的範例程式碼示範下列功能：

* 如何使用[microsoft.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)透過 Azure Active Directory 取得存取權杖。

* 如何在後續的查詢 API 要求的`Authorization`標頭中傳遞該取得的存取權杖。 

* 此範例會呼叫每個 GA 查詢 Api，以示範如何對進行 HTTP 要求：
    * [取得環境 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api)以傳回使用者可存取的環境
    * [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [取得環境中繼資料 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api)以取出環境中繼資料
    * [取得環境事件 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [取得環境匯總 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* 如何使用 WSS 與 GA 查詢 Api 互動，以訊息：

   * [取得環境事件資料流程 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [取得環境匯總資料流程 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>必要條件和設定

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. 布建[GA Azure 時間序列深入解析](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)環境。
1. 如[驗證和授權](time-series-insights-authentication-and-authorization.md)中所述，設定您的 Azure 時間序列深入解析環境以進行 Azure Active Directory。 
1. 安裝必要的專案相依性。
1. 以適當的環境識別碼取代每個 **#DUMMY #** ，以編輯下面的範例程式碼。
1. 在 Visual Studio 內執行程式碼。

## <a name="project-dependencies"></a>專案相依性

建議使用最新版本的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -版本 16.4.2 +

範例程式碼有兩個必要的相依性：

* [Microsoft.identitymodel.](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 3.13.9 套件。
* [Newtonsoft. Json](https://www.nuget.org/packages/Newtonsoft.Json) -9.0.1 封裝。

選取 [**組建** > **組建方案**] 選項，以在 Visual Studio 2019 中下載套件。

或者，使用[NuGet 2.12 +](https://www.nuget.org/)新增套件：

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解查詢，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 閱讀如何[使用用戶端 SDK 將 JavaScript 應用程式連接](https://github.com/microsoft/tsiclient)到時間序列深入解析。
