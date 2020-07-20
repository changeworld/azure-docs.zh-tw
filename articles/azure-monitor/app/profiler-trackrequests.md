---
title: 使用 Azure Application Insights 撰寫程式碼來追蹤要求 | Microsoft Docs
description: 使用 Application Insights 撰寫程式碼來追蹤要求，以取得要求的設定檔。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c59cbe852a91a91c7b3adb4452328700ec718a82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671591"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>使用 Application Insights 撰寫程式碼來追蹤要求

若要在 [效能] 頁面上檢視應用程式的設定檔，Azure Application Insights 必須追蹤對應用程式的要求。 Application Insights 可針對在已經過檢測的架構中建置的應用程式，自動追蹤其要求。 ASP.NET 與 ASP.NET Core 是其中的兩個範例。 

對於其他應用程式，例如 Azure 雲端服務背景工作角色和 Service Fabric 無狀態 API，您必須撰寫程式碼向 Application Insights 指出何處是您要求的開頭與結尾。 在您撰寫此程式碼之後，要求遙測資料就會傳送至 Application Insights。 您可以在 [效能] 頁面上檢視遙測資料，而系統將會為這些要求收集設定檔。 

若要手動追蹤要求，請執行下列作業：

  1. 在應用程式存留期早期新增下列程式碼：  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      如需這個全域檢測金鑰設定的詳細資訊，請參閱[搭配 Application Insights 使用 Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)。  

  1. 針對您想要檢測的任何程式碼片段，在其周圍新增 `StartOperation<RequestTelemetry>` **using** 陳述式，如下列範例所示：

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        不支援在另一個 `StartOperation<RequestTelemetry>` 範圍內呼叫 `StartOperation<RequestTelemetry>`。 您可以改為在巢狀範圍中使用 `StartOperation<DependencyTelemetry>`。 例如：  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
