---
title: Azure Application Insights 遙測資料模型 | Microsoft Docs
description: Application Insights 資料模型概觀
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: fd5848eb44ecd32612943662b5a4fd0a5091cd9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91461286"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights 遙測資料模型

[Azure Application Insights](./app-insights-overview.md) 會將遙測從您的 Web 應用程式傳送到 Azure 入口網站，以便您可以分析應用程式的效能和使用量。 遙測模型已經過標準化，如此就可以建立平台與無關語言的監視。 

Application Insights 所收集的資料會建立一般應用程式執行模式的模型︰

![Application Insights 應用程式模型](./media/data-model/application-insights-data-model.png)

下列遙測類型可用來監視您應用程式的執行。 Application Insights SDK 通常會從 Web 應用程式架構自動收集下列三種類型：

* [**要求**](data-model-request-telemetry.md)：產生以記錄應用程式所接收的要求。 例如，Application Insights Web SDK 會針對您 Web 應用程式接收的每個 HTTP 要求產生「要求」遙測項目。 

    **作業**是處理要求的執行緒。 您也可以[撰寫程式碼](./api-custom-events-metrics.md#trackrequest)來監視其他作業類型，例如，定期處理資料之 Web 工作或函式中的「喚醒」。  每個作業都有識別碼。 此識別碼可用來將應用程式處理要求時產生的所有遙測加以[分組](./correlation.md)。 每個作業可能會成功或失敗，而且有持續時間。
* [**例外狀況**](data-model-exception-telemetry.md)：通常表示造成作業失敗的例外狀況。
* [**相依性**](data-model-dependency-telemetry.md)：代表從您的應用程式對外部服務或儲存體的呼叫，例如 REST API 或 SQL。 在 ASP.NET 中，SQL 的相依性呼叫是由 `System.Data` 所定義。 HTTP 端點的呼叫是由 `System.Net` 所定義。 

Application Insights 針對自訂遙測提供三種其他資料類型：

* [追蹤](data-model-trace-telemetry.md)：可直接使用或透過配接器，以您所熟悉的檢測架構 (例如 `Log4Net` 或 `System.Diagnostics`) 來實作診斷記錄。
* [事件](data-model-event-telemetry.md)：通常用於擷取使用者與您服務的互動，藉以分析使用模式。
* [計量](data-model-metric-telemetry.md)：用於報告定期的純量測量。

每個遙測專案都可以定義 [內容資訊](data-model-context.md) ，例如應用程式版本或使用者會話識別碼。內容是一組強型別字段，會解除封鎖某些案例。 正確初始化應用程式版本之後，Application Insights 可以偵測與重新部署相互關聯的應用程式行為中是否有新模式。 工作階段識別碼可用來計算中斷或某個問題對使用者造成的影響。 計算特定失敗相依性、錯誤追蹤或重大例外狀況之工作階段識別碼值的相異計數，即可充分了解影響。

Application Insights 遙測模型會定義將遙測與其所屬作業[相互關聯](./correlation.md)的方式。 例如，要求會發出 SQL Database 呼叫，並記錄診斷資訊。 針對會往回繫結至要求遙測的遙測項目，您可以設定這些遙測項目的相互關聯內容。

## <a name="schema-improvements"></a>結構描述的增強功能

Application Insights 資料模型是簡單且基本但功能強大的方式，可建立您應用程式遙測的模型。 我們盡量保持模型可簡單且輕鬆地支援重要的案例，並擴充結構描述以供進階使用。

[若要報告資料模型或架構問題和建議，請使用我們的 GitHub 存放庫](https://github.com/microsoft/ApplicationInsights-dotnet/issues/new/choose)。

## <a name="next-steps"></a>後續步驟

- [撰寫自訂遙測](./api-custom-events-metrics.md)
- 了解如何[擴充和篩選遙測](./api-filtering-sampling.md)。
- 使用[取樣](./sampling.md)，根據資料模型將遙測量降到最低。
- 查看 Application Insights 支援的[平台](./platforms.md)。

