---
title: 使用 C# 查詢預覽環境的資料 - Azure 時間序列見解 |微軟文檔
description: 瞭解如何使用用 C# 編寫的應用從 Azure 時間序列見解環境查詢資料。
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
ms.openlocfilehash: 76e3ac85a6725976ebd14dac1805079613c94ec6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76980982"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用 C 查詢 Azure 時間序列見解預覽環境的資料#

此 C# 示例演示如何在 Azure 時間序列預覽環境中查詢預覽[資料訪問 API](https://docs.microsoft.com/rest/api/time-series-insights/preview)中的資料。

> [!TIP]
> 在 上[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)查看預覽 C# 代碼示例。

## <a name="summary"></a>總結

下面的示例代碼演示了以下功能：

* 支援從 Azure 自動[Rest](https://github.com/Azure/AutoRest)自動生成 SDK。
* 如何使用[Microsoft.標識模型.用戶端.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)通過 Azure 活動目錄獲取訪問權杖。
* 如何在後續資料訪問 API 請求的`Authorization`標頭中傳遞獲取的訪問權杖。 
* 該示例提供了一個主控台介面，演示如何向 以下項發出 HTTP 要求：

    * [預覽環境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [獲取環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)並[獲取事件架構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [預覽查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [獲取事件 API、](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)[獲取系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)和[獲取聚合系列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [時間序列模型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [獲取層次結構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get)和[層次結構批次處理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [獲取類型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)和[類型批次處理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [獲取實例 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get)和[實例批次處理 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 高級[搜索](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features)和[TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)功能。

## <a name="prerequisites-and-setup"></a>先決條件和設置

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. [預配預覽 Azure 時間序列見解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)環境。
1. 為 Azure 活動目錄配置 Azure 時間序列見解環境，如[身份驗證和授權](time-series-insights-authentication-and-authorization.md)中所述。 
1. 運行[Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md)中指定的[GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat)以生成時間序列見解預覽用戶端依賴項。
1. 打開解決方案`TSIPreviewDataPlaneclient.sln`並將其設置為`DataPlaneClientSampleApp`Visual Studio 中的預設專案。
1. 使用[下面](#project-dependencies)描述的步驟安裝所需的專案依賴項，並將示例編譯為可`.exe`執行檔。
1. 按兩下`.exe`檔即可運行該檔。

## <a name="project-dependencies"></a>專案相依性

建議您使用最新版本的視覺化工作室：

* [視覺工作室 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2*

示例代碼具有幾個必需的依賴項，可以在[包](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)中查看。

通過選擇 **"生成生成** > **解決方案**"選項，下載 Visual Studio 2019 中的套裝軟體。 

或者，使用[NuGet 2.12+](https://www.nuget.org/)添加每個包。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 可以在不更改預設環境變數的情況下執行代碼示例。
> * 代碼示例將編譯到 .NET 可執行主控台應用。

## <a name="next-steps"></a>後續步驟

- 要瞭解有關查詢的更多資訊，請閱讀[查詢 API 引用](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)。

- 瞭解如何[使用用戶端 SDK 將 JavaScript 應用連接到](https://github.com/microsoft/tsiclient)時間序列見解。
