---
title: 使用 Application Insights 分析即時 Azure Service Fabric 應用程式
description: 針對 Service Fabric 應用程式啟用 Profiler
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 317c02fe03a56ea3cd3a98b00f8a0080b3917108
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930276"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>使用 Application Insights 來分析即時 Azure Service Fabric 應用程式

您也可以在這些服務上部署 Application Insights Profiler：
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>設定環境部署定義

Application Insights Profiler 會隨附於 Azure 診斷。 您可以使用 Azure Resource Manager 範本為 Service Fabric 叢集安裝 Azure 診斷擴充功能。 取得[將 Azure 診斷安裝在 Service Fabric 叢集上的範本](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)。

若要設定環境，請採取下列動作：

1. Profiler 支援 .NET Framework 和 .Net Core。 如果您使用 .NET Framework，請確定您是使用 [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 或更新版本。 確認部署的作業系統是 `Windows Server 2012 R2` 或更新版本已足夠。 Profiler 支援 .NET Core 2.1 和更新版本的應用程式。

1. 在部署範本檔案中搜尋 [Azure 診斷](../platform/diagnostics-extension-overview.md)擴充功能。

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

      如需將診斷擴充功能新增至部署範本的詳細資訊，請參閱[使用 Windows VM 和 Azure Resource Manager 範本的監視和診斷](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)。

1. 使用 Azure Resource Manager 範本部署 Service Fabric 叢集。  
  如果設定全都正確，就會在安裝 Azure 診斷擴充功能時，安裝並啟用 Application Insights Profiler。 

1. 在 Service Fabric 應用程式中新增 Application Insights。  
  為了讓 Profiler 收集要求的設定檔，您的應用程式必須追蹤 Application Insights 的作業。 針對無狀態 Api，您可以參考 [追蹤要求以進行程式碼剖析](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)的指示。 如需在其他類型的應用程式中追蹤自訂作業的詳細資訊，請參閱 [使用 Application Insights .NET SDK 追蹤自訂作業](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)。

1. 重新部署應用程式。


## <a name="next-steps"></a>後續步驟

* 產生應用程式的流量 (例如，啟動[可用性測試](monitor-web-app-availability.md))。 然後，等待 10 到 15 分鐘，讓追蹤開始傳送到 Application Insights 執行個體。
* 請參閱 Azure 入口網站中的 [Profiler 追蹤](profiler-overview.md?toc=/azure/azure-monitor/toc.json) 。
* 如需 Profiler 問題的疑難排解說明，請參閱 [Profiler 疑難排解](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)。
