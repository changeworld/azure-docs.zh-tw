---
title: '使用 c # 從 Gen2 環境查詢資料-Azure 時間序列深入解析 |Microsoft Docs'
description: '瞭解如何使用以 c # 撰寫的應用程式來查詢 Azure 時間序列深入解析 Gen2 環境中的資料。'
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/14/2020
ms.custom: seodec18
ms.openlocfilehash: ac65f1927aa890e5eaed757d7db054f2c2bbad3d
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243729"
---
# <a name="query-data-from-the-azure-time-series-insights-gen2-environment-using-c-sharp"></a>使用 C 銳利查詢 Azure 時間序列深入解析 Gen2 環境中的資料

此 c # 範例示範如何在 Azure 時間序列深入解析 Gen2 環境中，從 [Gen2 資料存取 api](https://docs.microsoft.com/rest/api/time-series-insights/reference-data-access-overview) 查詢資料。

> [!TIP]
> View Gen2 c # 程式碼範例，網址為 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) 。

## <a name="summary"></a>摘要

以下範例程式碼會示範下列功能：

* [Azure AutoRest](https://github.com/Azure/AutoRest) 中的 SDK 自動產生支援。
* 如何使用 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 透過 Azure Active Directory 取得存取權杖。
* 如何在後續資料存取 API 要求的 `Authorization` 標頭中傳遞該取得的存取權杖。
* 此範例會提供主控台介面，示範如何對下列各項進行 HTTP 要求：
  * [Gen2 環境 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-environments-apis)
    * [取得環境可用性 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability) (機器翻譯) 和[取得事件結構描述 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema) (機器翻譯)
  * [Gen2 查詢 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis)
    * [取得事件 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents) (機器翻譯)、[取得數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries) (機器翻譯) 和[取得彙總數列 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries) (機器翻譯)
  * [時間序列模型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries) (機器翻譯)
    * [取得階層 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies) (機器翻譯) 和[階層批次 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch) (機器翻譯)
    * [取得類型 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes) (機器翻譯) 和[類型批次 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch) (機器翻譯)
    * [取得執行個體 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances) (機器翻譯) 和[執行個體批次 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch) (機器翻譯)

* 進階[搜尋](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#search-features) (機器翻譯) 和 [TSX](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) (機器翻譯) 功能。

## <a name="prerequisites-and-setup"></a>先決條件和設定

編譯及執行範例程式碼之前，您必須先完成下列步驟：

1. 布建[Gen2 Azure 時間序列深入解析](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-how-to-manage#create-the-environment)環境。
1. 針對 Azure Active Directory 設定 Azure 時間序列見解環境，如[驗證和授權](time-series-insights-authentication-and-authorization.md)中所述。
1. 執行[Readme.md](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/Readme.md)中指定的[GenerateCode.bat](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClient/GenerateCode.bat) ，以產生 Azure 時間序列深入解析 Gen2 用戶端相依性。
1. 在 Visual Studio 中開啟 `TSIPreviewDataPlaneclient.sln` 方案，並將 `DataPlaneClientSampleApp` 設定為預設專案。
1. 使用[以下](#project-dependencies)所述步驟來安裝必要的專案相依性，並將範例編譯為可執行檔 `.exe`。
1. 按兩下 `.exe` 檔案加以執行。

## <a name="project-dependencies"></a>專案相依性

建議使用最新版的 Visual Studio：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - Version 16.4.2+

本範例程式碼有數個必要的相依性，您可在 [packages.config](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/packages.config) 檔案中檢視這些相依性。

選取 [建置] > [建置方案]，以下載 Visual Studio 2019 中的套件。

或者，使用 [NuGet 2.12+](https://www.nuget.org/) 新增每個套件。 例如：

* `dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory --version 4.5.1`

## <a name="c-sample-code"></a>C# 範例程式碼

C # 範例程式碼可在[csharpquery-範例](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample)中找到

> [!NOTE]
>
> * 您可在不改變預設環境變數的情況下執行此程式碼範例。
> * 此程式碼範例會編譯為 .NET 可執行檔主控台應用程式。

## <a name="next-steps"></a>後續步驟

* 若要深入了解查詢，請參閱[查詢 API 參考](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis) (機器翻譯)。

* 閱讀如何 [使用用戶端 SDK 將 JavaScript 應用程式連接](https://github.com/microsoft/tsiclient) 到 Azure 時間序列深入解析。
