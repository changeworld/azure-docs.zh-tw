---
title: 在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具 |Microsoft Docs
description: 在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6928da704236c4bb5492f99a4a5327bf297a323d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84676832"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具

快照偵錯工具目前適用于在 Windows 服務方案上的 Azure App Service 上執行的 ASP.NET 和 ASP.NET Core 應用程式。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>啟用快照偵錯工具
若要啟用應用程式的快照偵錯工具，請遵循下列指示。 如果您正在執行不同類型的 Azure 服務，以下是在其他支援的平臺上啟用快照偵錯工具的指示：
* [Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

如果您使用的是 .NET Core 的預覽版本，請先遵循針對[其他環境啟用快照偵錯工具](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)的指示，先將[microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件包含在應用程式中，然後完成下列其餘指示。 

Application Insights 快照偵錯工具會預先安裝為應用程式服務執行時間的一部分，但您必須開啟它，才能取得 App Service 應用程式的快照集。 當您部署應用程式之後，即使您已在原始程式碼中包含 Application Insights SDK，請遵循下列步驟來啟用快照偵錯工具。

1. 流覽至您 App Service 的 Azure [控制台]。
2. 移至 [**設定] > Application Insights** ] 頁面。

   ![在 App Service 入口網站上啟用 Application Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 請遵循頁面上的指示來建立新的資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定快照偵錯工具的兩個參數都是**On**。

   ![新增 App Insights 網站延伸模組][Enablement UI]

4. 快照偵錯工具現在已使用應用程式服務應用程式設定來啟用。

    ![快照偵錯工具的應用程式設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>停用快照偵錯工具

遵循與 [**啟用快照偵錯工具**] 相同的步驟，但將快照偵錯工具的兩個交換器切換為 [**關閉**]。
我們建議您在所有應用程式上都啟用快照偵錯工具，以簡化應用程式例外狀況的診斷。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

如需 Azure App Service，您可以在 Azure Resource Manager 範本中設定應用程式設定，以啟用快照偵錯工具和 Profiler。 您會將包含應用程式設定的 config 資源新增為網站的子資源：

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  "tags": { 
    "[concat('hidden-related:', resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName')))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[parameters('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "name": "appsettings",
      "type": "config",
      "dependsOn": [
        "[parameters('webSiteName')]",
        "[concat('AppInsights', parameters('webSiteName'))]"
      ],
      "properties": {
        "APPINSIGHTS_INSTRUMENTATIONKEY": "[reference(resourceId('Microsoft.Insights/components', concat('AppInsights', parameters('webSiteName'))), '2014-04-01').InstrumentationKey]",
        "APPINSIGHTS_PROFILERFEATURE_VERSION": "1.0.0",
        "APPINSIGHTS_SNAPSHOTFEATURE_VERSION": "1.0.0",
        "DiagnosticServices_EXTENSION_VERSION": "~3",
        "ApplicationInsightsAgent_EXTENSION_VERSION": "~2"
      }
    }
  ]
},
```

## <a name="next-steps"></a>後續步驟

- 產生可觸發例外狀況的應用程式流量。 然後，等候10到15分鐘，讓快照集傳送至 Application Insights 實例。
- 請參閱 Azure 入口網站中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)集。
- 如需疑難排解快照偵錯工具問題的協助，請參閱[快照偵錯工具疑難排解](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

