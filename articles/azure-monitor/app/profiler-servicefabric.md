---
title: 使用應用程式見解設定檔即時 Azure 服務結構應用
description: 針對 Service Fabric 應用程式啟用 Profiler
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671608"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Service Fabric 應用程式

您也可以在這些服務上部署 Application Insights Profiler：
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>設定環境部署定義

Application Insights Profiler 會隨附於 Azure 診斷。 您可以使用 Azure Resource Manager 範本為 Service Fabric 叢集安裝 Azure 診斷擴充功能。 取得[將 Azure 診斷安裝在 Service Fabric 叢集上的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。

若要設定環境，請採取下列動作：

1. 探測器支援 .NET 框架和 .Net 核心。 如果您使用的是 .NET 框架，請確保使用的是[.NET 框架 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)或更高版本。 這足以確認已部署的作業系統是`Windows Server 2012 R2`或更高版本。 探測器支援 .NET Core 2.1 和更新的應用程式。

1. 在部署範本檔案中搜尋 [Azure 診斷](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)擴充功能。

1. 新增下列 `SinksConfig` 區段作為 `WadCfg` 的子元素。 將 `ApplicationInsightsProfiler` 屬性值取代為您自己的 Application Insights 檢測金鑰：  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      如需將診斷擴充功能新增至部署範本的詳細資訊，請參閱[使用 Windows VM 和 Azure Resource Manager 範本的監視和診斷](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

1. 使用 Azure Resource Manager 範本部署 Service Fabric 叢集。  
  如果設定全都正確，就會在安裝 Azure 診斷擴充功能時，安裝並啟用 Application Insights Profiler。 

1. 在 Service Fabric 應用程式中新增 Application Insights。  
  對於探測器收集請求的設定檔，應用程式必須使用應用程式見解跟蹤操作。 對於無狀態 API，您可以引用有關[跟蹤分析請求](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)的說明。 有關跟蹤其他類型的應用中的自訂操作的詳細資訊，請參閱[使用應用程式見解 .NET SDK 跟蹤自訂操作](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)。

1. 重新部署應用程式。


## <a name="next-steps"></a>後續步驟

* 產生應用程式的流量 (例如，啟動[可用性測試](monitor-web-app-availability.md))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
* 請參閱 Azure 門戶中的[探測器跟蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json)。
* 如需 Profiler 問題的疑難排解說明，請參閱 [Profiler 疑難排解](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
