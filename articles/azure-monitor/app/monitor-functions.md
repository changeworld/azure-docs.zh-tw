---
title: 使用 Application Insights - Azure 監視器監視在 Azure Functions 上執行的應用程式 | Microsoft Docs
description: Azure 監視器會與在 Azure Functions 上執行的應用程式緊密整合，讓您能即時監視效能及找出應用程式的問題。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 6d790a6f02fdc07609e374639c6e452b9088262e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024566"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>使用 Azure 監視器的 Application Insights 監視 Azure Functions

[Azure Functions](../../azure-functions/functions-overview.md) 提供與 Azure Application Insights 的內建整合來監視函式。 

Application Insights 會收集記錄、效能和錯誤資料，並自動偵測效能異常。 Application Insights 隨附強大的分析工具，以協助您診斷問題，以及了解如何使用您的函式。 當您了解應用程式資料時，便可以持續改善效能和可用性。 您甚至可以在本機函式應用程式專案開發期間使用 Application Insights。 

所需的 Application Insights 檢測已內建於 Azure Functions 中。 您唯一需要的是有效的檢測金鑰，以將您的函式應用程式連線至 Application Insights 資源。 當您在 Azure 中建立函式應用程式資源時，應該將檢測金鑰新增至應用程式設定。 如果您的函式應用程式還沒有這個金鑰，您可以手動設定。 如需詳細資訊，請深入了解[監視 Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd)。

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Windows 上 JAVA 應用程式的分散式追蹤（公開預覽）

> [!IMPORTANT]
> 這項功能目前處於 Windows 上的 JAVA Azure Functions 公開預覽狀態，不支援 Linux 上的 JAVA Azure Functions 的分散式追蹤。 針對取用方案，其冷啟動時間為8-9 秒。

如果您的應用程式是以 JAVA 撰寫的，您可以從函式應用程式中檢視更豐富的資料，包括要求、相依性、記錄和計量。 此外有額外的資料可讓您看見與診斷端對端交易，及看見可彙總許多交易的應用程式對應，以顯示系統互動方式的拓撲檢視，及平均效能和錯誤率。

端對端診斷和應用程式對應可讓您看到單一交易/要求。 這兩個功能一起使用，對於尋找可靠性問題的根本原因，以及每個要求的效能瓶頸非常有幫助。

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>如何為 Java Function 應用程式啟用分散式追蹤？

瀏覽至函式應用程式 [概覽] 刀鋒視窗，移至 [設定]。 在 [應用程式設定] 下，按一下 [+ 新增應用程式設定]。 新增以下兩個具有下列值的應用程式設定，然後按一下左上方的 [儲存]。 大功告成！

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>後續步驟

* 深入了解[監視 Azure Functions](../../azure-functions/functions-monitoring.md)中有關監視的指示與資訊
* 了解[分散式追蹤](./distributed-tracing.md)概況
* 了解[應用程式對應](./app-map.md?tabs=net)對您的業務有何幫助
* 閱讀 [JAVA 應用程式的要求和相依性](./java-in-process-agent.md)
* 深入了解 [Azure 監視器](../overview.md) 與 [Application Insights](./app-insights-overview.md)
