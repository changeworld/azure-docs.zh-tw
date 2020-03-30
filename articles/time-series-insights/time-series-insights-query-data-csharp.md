---
title: 使用 C# 代碼查詢來自 GA 環境的資料 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何使用用 C# 編寫的自訂應用查詢 Azure 時序見解環境中的資料。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 9f7819974e3548baf5e10f0bf9a2d656d9412beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987966"
---
# <a name="query-data-from-the-azure-time-series-insights-ga-environment-using-c"></a>使用 C 查詢 Azure 時間序列見解 GA 環境的資料#

此 C# 示例演示如何使用[GA 查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)查詢 Azure 時間序列見解 GA 環境中的資料。

> [!TIP]
> 在 上[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample)查看 GA C# 代碼示例。

## <a name="summary"></a>總結

下面的示例代碼演示了以下功能：

* 如何使用[Microsoft.標識模型.用戶端.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)通過 Azure 活動目錄獲取訪問權杖。

* 如何在後續查詢 API 請求的標頭`Authorization`中傳遞獲取的訪問權杖。 

* 該示例調用每個 GA 查詢 API，演示如何向 以下部門發出 HTTP 要求：
    * [獲取環境 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environments-api)以返回使用者有權訪問的環境
    * [獲取環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-availability-api)
    * [獲取環境中繼資料 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-metadata-api)以檢索環境中繼資料
    * [獲取環境事件 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api)
    * [獲取環境聚合 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api)
    
* 如何使用 WSS 與 GA 查詢 API 進行交互，以消息：

   * [獲取環境事件流 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-streamed-api)
   * [獲取環境聚合流式處理 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-streamed-api)

## <a name="prerequisites-and-setup"></a>先決條件和設置

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. [預配 GA Azure 時間序列見解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started)環境。
1. 為 Azure 活動目錄配置 Azure 時間序列見解環境，如[身份驗證和授權](time-series-insights-authentication-and-authorization.md)中所述。 
1. 安裝所需的專案依賴項。
1. 通過將每個 **#DUMMY#** 替換為相應的環境識別碼，編輯下面的示例代碼。
1. 在視覺化工作室內執行代碼。

## <a name="project-dependencies"></a>專案相依性

建議您使用最新版本的視覺化工作室：

* [視覺工作室 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2*

示例代碼有兩個必需的依賴項：

* [微軟.身份模型.用戶端.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) - 3.13.9 包。
* [牛頓軟.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 9.0.1 封裝。

通過選擇 **"生成生成** > **解決方案**"選項，下載 Visual Studio 2019 中的套裝軟體。

或者，使用[NuGet 2.12+](https://www.nuget.org/)添加包 ：

* `dotnet add package Newtonsoft.Json --version 9.0.1`
* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 3.13.9`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs)]

## <a name="next-steps"></a>後續步驟

- 要瞭解有關查詢的更多資訊，請閱讀[查詢 API 引用](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)。

- 瞭解如何[使用用戶端 SDK 將 JavaScript 應用連接到](https://github.com/microsoft/tsiclient)時間序列見解。
