---
title: 使用 C# 查詢預覽環境的資料 - Azure 時間序列見解 :微軟文件
description: 瞭解如何使用用 C# 編寫的應用從 Azure 時間序列見解環境查詢數據。
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
ms.openlocfilehash: fbc2cbc29cb23a21e7d3713091fc22f01bb1b15a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379819"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用 C 查詢 Azure 時間序列的預覽環境的資料#

此 C# 範例展示如何在 Azure 時間序列預覽環境中查詢預覽[資料存取 API](https://docs.microsoft.com/rest/api/time-series-insights/preview)中的資料。

> [!TIP]
> 在[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)上 查看預覽 C# 代碼範例。

## <a name="summary"></a>摘要

下面的範例代碼展示以下功能:

* 支援從 Azure 自動[Rest](https://github.com/Azure/AutoRest)自動生成 SDK。
* 如何使用[Microsoft.標識模型.用戶端.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)通過 Azure 活動目錄獲取訪問權杖。
* 如何在後續數據訪問 API`Authorization`請求的標頭中傳遞獲取的訪問權杖。 
* 該範例提供控制台介面,簡報如何向 以下項發出 HTTP 請求:

    * [預覽環境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)並[取得事件架構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [預覽查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [取得事件 API,](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)[取得系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)與[取得集合的 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [時間序列模型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [取得層次結構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get)和[層次結構批次處理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [取得類型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)與[類型處理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [取得實體 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get)與[實體處理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 高級[搜索](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features)和[TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)功能。

## <a name="prerequisites-and-setup"></a>先決條件與設定

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. [預配預覽 Azure 時間序列見解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)環境。
1. 為 Azure 活動目錄配置 Azure 時間序列見解環境,如[身份驗證和授權](time-series-insights-authentication-and-authorization.md)中所述。 
1. 運行[Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md)中指定的[GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat)以生成時間序列見解預覽客戶端依賴項。
1. 打開解決方案`TSIPreviewDataPlaneclient.sln`並將其設置`DataPlaneClientSampleApp`為 Visual Studio 中的預設專案。
1. 使用[下面](#project-dependencies)描述的步驟安裝所需的項目依賴項,並將示例編譯為`.exe`可 執行檔。
1. 按兩`.exe`下 檔案即可運行該檔。

## <a name="project-dependencies"></a>專案相依性

建議您使用最新版本的可視化工作室:

* [視覺工作室 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2*

範例代碼具有幾個必需的依賴項,可以在[包](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)中查看。

通過選擇 **「生成生成** > **解決方案**」選項,下載 Visual Studio 2019 中的套件。 

或者,使用[NuGet 2.12+](https://www.nuget.org/)添加每個包。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 可以在不更改預設環境變數的情況下執行代碼範例。
> * 代碼示例將編譯到 .NET 可執行主控台應用。

## <a name="next-steps"></a>後續步驟

- 要瞭解有關查詢的更多資訊,請閱讀[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)。

- 瞭解如何[使用用戶端 SDK 將 JavaScript 應用連接到](https://github.com/microsoft/tsiclient)時間序列見解。
