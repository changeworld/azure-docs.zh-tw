---
title: 使用 C# 程式碼查詢來自 GA 環境的資料 - Azure 時間序列見解 。微軟文件
description: 瞭解如何使用用 C# 編寫的自訂應用查詢 Azure 時序見解環境中的數據。
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383877"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>使用 C 查詢 Azure 時間序列的解 GA 環境的資料#

此 C# 範例展示如何使用[GA 查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)查詢 Azure 時間序列見解 GA 環境中的數據。

> [!TIP]
> 在[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)上查看 GA C# 代碼範例。

## <a name="summary"></a>摘要

下面的範例代碼展示以下功能:

* 如何使用[Microsoft.標識模型.用戶端.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)通過 Azure 活動目錄獲取訪問權杖。

* 如何在後續查詢 API 請求的`Authorization`標頭 中傳遞獲取的訪問權杖。 

* 該範例呼叫每個 GA 查詢 API,簡報如何向 以下部門發出 HTTP 請求:
    * [取得環境 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api)以傳回使用者有權存取的環境
    * [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [取得環境中繼資料 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api)以檢查索環境中繼資料
    * [取得環境事件 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [取得環境集合API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* 如何使用 WSS 與 GA 查詢 API 進行互動,以訊息:

   * [取得環境事件串流 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [取得環境導取 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>先決條件與設定

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. [預配 GA Azure 時間序列見解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)環境。
1. 為 Azure 活動目錄配置 Azure 時間序列見解環境,如[身份驗證和授權](time-series-insights-authentication-and-authorization.md)中所述。 
1. 安裝所需的項目依賴項。
1. 通過將每個 **#DUMMY#** 替換為相應的環境識別符,編輯下面的範例代碼。
1. 在可視化工作室內執行代碼。

## <a name="project-dependencies"></a>專案相依性

建議您使用最新版本的可視化工作室:

* [視覺工作室 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2*

範例代碼有兩個必需的依賴項:

* [微軟.身份模型.用戶端.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9 包。
* [牛頓軟.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 封裝。

通過選擇 **「生成生成** > **解決方案**」選項,下載 Visual Studio 2019 中的套件。

或使用[NuGet 2.12+](https://www.nuget.org/)新增套件 :

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>後續步驟

- 要瞭解有關查詢的更多資訊,請閱讀[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 瞭解如何[使用用戶端 SDK 將 JavaScript 應用連接到](https://github.com/microsoft/tsiclient)時間序列見解。
