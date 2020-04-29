---
title: '使用 c # 從預覽環境查詢資料-Azure 時間序列深入解析 |Microsoft Docs'
description: '瞭解如何使用以 c # 撰寫的應用程式來查詢 Azure 時間序列深入解析環境中的資料。'
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81379819"
---
# <a name="query-data-from-the-azure-time-series-insights-preview-environment-using-c"></a>使用 C 從 Azure 時間序列深入解析預覽環境查詢資料#

此 c # 範例示範如何在 Azure 時間序列深入解析預覽環境中，從[預覽資料存取 api](https://docs.microsoft.com/rest/api/time-series-insights/preview)查詢資料。

> [!TIP]
> 查看預覽 c # 程式碼[https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-preview-sample)範例，網址為。

## <a name="summary"></a>[摘要]

下面的範例程式碼示範下列功能：

* 支援從[Azure AutoRest](https://github.com/Azure/AutoRest)自動產生 SDK。
* 如何使用[microsoft.identitymodel](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)透過 Azure Active Directory 取得存取權杖。
* 如何在後續的資料存取 API 要求的`Authorization`標頭中傳遞該取得的存取權杖。 
* 此範例會提供主控台介面，示範如何對進行 HTTP 要求：

    * [預覽環境 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#preview-environments-apis)
        * [取得環境可用性 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)並[取得事件架構 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/geteventschema)
    * [預覽查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/preview#query-apis)
        * [取得事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getevents)、[取得數列 Api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#getseries)，並[取得匯總數列 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
    * [時間序列模型 Api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/execute#aggregateseries)
        * [取得階層 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/get)和階層[Batch api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeserieshierarchies/executebatch)
        * [取得類型 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/get)和[類型 Batch api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch)
        * [取得實例 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/get)和[實例批次 api](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriesinstances/executebatch)
* 先進的[搜尋](https://docs.microsoft.com/rest/api/time-series-insights/preview#search-features)和[TSX](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)功能。

## <a name="prerequisites-and-setup"></a>必要條件和設定

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. 布建[預覽 Azure 時間序列深入解析](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)環境。
1. 如[驗證和授權](time-series-insights-authentication-and-authorization.md)中所述，設定您的 Azure 時間序列深入解析環境以進行 Azure Active Directory。 
1. 依照[Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/Readme.md)中的指定執行[GenerateCode](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClient/GenerateCode.bat) ，以產生時間序列深入解析預覽用戶端相依性。
1. 開啟`TSIPreviewDataPlaneclient.sln`方案，並在`DataPlaneClientSampleApp` Visual Studio 中將設定為預設專案。
1. 使用[下面](#project-dependencies)所述的步驟來安裝必要的專案相依性，並將範例`.exe`編譯為可執行檔。
1. `.exe`按兩下以執行檔案。

## <a name="project-dependencies"></a>專案相依性

建議使用最新版本的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -版本 16.4.2 +

範例程式碼有數個必要的相依性，可以在[封裝 .config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/csharp-tsi-preview-sample/DataPlaneClientSampleApp/packages.config)檔案中查看。

選取 [**組建** > **組建方案**] 選項，以在 Visual Studio 2019 中下載套件。 

或者，使用[NuGet 2.12 +](https://www.nuget.org/)新增每個套件。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 範例程式碼

[!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-preview-sample/DataPlaneClientSampleApp/Program.cs)]

> [!NOTE]
> * 您可以在不改變預設環境變數的情況下執行程式碼範例。
> * 程式碼範例會編譯成 .NET 可執行檔主控台應用程式。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解查詢，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/preview-query)。

- 閱讀如何[使用用戶端 SDK 將 JavaScript 應用程式連接](https://github.com/microsoft/tsiclient)到時間序列深入解析。
