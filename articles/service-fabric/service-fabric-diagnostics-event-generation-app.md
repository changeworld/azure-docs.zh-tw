---
title: Azure Service Fabric 應用層級監視
description: 了解用來監視和診斷 Azure Service Fabric 叢集的應用程式與服務層級事件和記錄。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 97c3be391dfbee7301ea47bf7234a9549d373370
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75464714"
---
# <a name="application-logging"></a>應用程式記錄檔

檢測程式碼不僅是深入了解您的使用者的一種方式，也是了解應用程式中是否存在問題，以及診斷需要修正問題之內容的唯一方法。 雖然技術上可以將偵錯工具連線至生產服務，但這不是常見的做法。 因此，取得詳細的檢測資料很重要。

某些產品會自動檢測您的程式碼。 雖然這些產品的效果不錯，但幾乎都還是需要手動檢測才能專屬於您的商務邏輯。 最後您還是要有足夠資訊，才能以抽絲剝繭的方式對應用程式進行偵錯。 Service Fabric 應用程式可以使用任何記錄架構進行檢測。 此文件說明一些檢測程式碼的不同方法，以及在何種情況下應選擇什麼方法。 

如需有關如何使用這些建議的範例，請參閱 [在您的 Service Fabric 應用程式中新增記錄](service-fabric-how-to-diagnostics-log.md)。

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights 與 Service Fabric 已有現成的豐富整合。 使用者可以新增 AI Service Fabric nuget 套件，接收在 Azure 入口網站中建立並收集的可檢視資料和記錄。 此外，也建議使用者新增他們自己的遙測，用來診斷和偵錯他們的應用程式，以及追蹤應用程式的哪些服務和功能最常使用。 SDK 中的 [TelemetryClient](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient?view=azure-dotnet) 類別提供許多追蹤應用程式遙測的方式。 請看[監視與診斷 .NET 應用程式](service-fabric-tutorial-monitoring-aspnet.md)教學課程中的範例，了解如何檢測以及將 Application Insights 加入應用程式中。

## <a name="eventsource"></a>EventSource

當您在 Visual Studio 中從範本建立 Service Fabric 解決方案時，將會產生 **EventSource** 衍生類別 (**ServiceEventSource** 或 **ActorEventSource**)。 建立的範本可讓您為應用程式或服務新增事件。 **EventSource**名稱**必須**是唯一的，而且應該從預設範本字串 MyCompany-方案專案中重新命名 &lt; &gt; - &lt; &gt; 。 多個同名的 **EventSource** 定義會導致執行階段發生問題。 每個已定義事件都必須有獨一無二的識別碼。 如果識別碼並非獨一無二，會發生執行階段失敗。 有些組織會預先指派識別碼的值範圍，以避免不同開發小組之間用法不一致。 如需詳細資訊，請參閱 [Vance 的部落格](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)或 [MSDN 文件](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)。

## <a name="aspnet-core-logging"></a>ASP.NET Core 記錄

必須仔細規劃您檢測程式碼的方式。 正確規劃檢測有助於避免可能使程式碼基底不穩定，導致需要重新檢測程式碼。 為了降低風險，開發人員可以選擇檢測程式庫，例如 Microsoft ASP.NET Core 中的 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)。 ASP.NET Core 提供 [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) 介面，可搭配您選擇的提供者一起使用，讓現有程式碼所受的影響降到最低。 您可以在 Windows 和 Linux 上使用 ASP.NET Core 中的程式碼，也可以使用完整 .NET Framework 中的程式碼，使檢測程式碼標準化。

## <a name="next-steps"></a>後續步驟

在您選擇好要用來檢測應用程式和服務的記錄提供者之後，還需要彙總這些記錄和事件，才能將其傳送到任何分析平台。 請閱讀 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 與 [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)，以深入了解一些 Azure 監視器建議的選項。
