---
title: 在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具 |Microsoft Docs
description: 在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 421f80493a9cb88e8bbbddc06aa9a24042b64b17
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695465"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具

快照偵錯工具目前適用于在 Windows 服務方案 Azure App Service 上執行的 ASP.NET 和 ASP.NET Core 應用程式。 使用快照偵錯工具時，建議您在基本服務層級或更高版本上執行您的應用程式。 在大部分的應用程式中，免費和共用的服務層級沒有足夠的記憶體或磁碟空間來儲存快照集。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a> 啟用快照偵錯工具
若要為應用程式啟用快照偵錯工具，請遵循下列指示。

如果您正在執行不同類型的 Azure 服務，則以下是在其他支援的平臺上啟用快照偵錯工具的指示：
* [Azure 函式](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> 如果您使用的是 .NET Core 預覽版本，或您的應用程式參考 Application Insights SDK （直接或間接透過相依的元件），請先遵循針對 [其他環境啟用快照偵錯工具](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) 的指示，先將 [microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件包含在應用程式中，然後完成下列其餘的指示。 

快照偵錯工具已預先安裝為 App Service 執行時間的一部分，但您需要將其開啟，才能取得 App Service 應用程式的快照集。

部署應用程式之後，請遵循下列步驟來啟用快照偵錯工具：

1. 流覽至您 App Service 的 [Azure 控制台]。
2. 移至 [ **設定] > Application Insights** ] 頁面。

   ![在 App Service 入口網站上啟用 Application Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 請依照頁面上的指示建立新的資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定快照偵錯工具的兩個交換器都已 **開啟**。

   ![新增 App Insights 網站延伸模組][Enablement UI]

4. 現在會使用應用程式服務應用程式設定來啟用快照偵錯工具。

    ![快照偵錯工具的應用程式設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>停用快照偵錯工具

遵循與 **啟用快照偵錯工具** 相同的步驟，但將這兩個參數切換為 [ **關閉**] 快照偵錯工具。

建議您在所有應用程式上都已啟用快照偵錯工具，以簡化應用程式例外狀況的診斷。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

針對 Azure App Service，您可以在 Azure Resource Manager 範本內設定應用程式設定，以啟用快照偵錯工具和 Profiler，請參閱下列範本程式碼片段：

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

- 產生可觸發例外狀況的應用程式流量。 然後，等候10到15分鐘，將快照集傳送至 Application Insights 實例。
- 請參閱 Azure 入口網站中的 [快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) 集。
- 如需疑難排解快照偵錯工具問題的協助，請參閱 [快照偵錯工具疑難排解](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

