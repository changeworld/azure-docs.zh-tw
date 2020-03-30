---
title: 在 Azure 應用服務中為 .NET 應用啟用快照調試器 |微軟文檔
description: 在 Azure 應用服務中為 .NET 應用啟用快照調試器
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8af688e38003e0613a06d7d8622ce279a3838589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298280"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>在 Azure 應用服務中為 .NET 應用啟用快照調試器

快照調試器當前適用于在 Windows 服務方案上在 Azure 應用服務上運行的ASP.NET和ASP.NET核心應用。

## <a name="enable-snapshot-debugger"></a><a id="installation"></a>啟用快照調試器
要為應用啟用快照調試器，請按照以下說明操作。 如果運行不同類型的 Azure 服務，以下是在其他受支援平臺上啟用快照調試器的說明：
* [Azure 雲服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 服務交換矩陣服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [本地虛擬或物理電腦](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

如果您使用的是 .NET Core 的預覽版本，請按照[啟用其他環境的快照調試器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)的說明首先包括[Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套裝程式，然後完成下面的其餘說明。 

應用程式見解快照調試器作為應用服務運行時的一部分預先安裝，但您需要將其打開才能獲取應用服務應用的快照。 部署應用後，即使您在原始程式碼中包含應用程式見解 SDK，請按照以下步驟啟用快照調試器。

1. 移至 Azure 入口網站中的 [App Service]**** 窗格。
2. 瀏覽至 [設定] > [Application Insights]**** 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 還要確保快照調試器的兩個交換器都**打開**。

   ![新增 App Insights 網站延伸模組][Enablement UI]

4. 快照調試器現在使用應用服務應用設置啟用。

    ![快照調試器的應用設置][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>禁用快照調試器

按照與**啟用快照調試器**相同的步驟，但將快照調試器的兩個開關切換到 **"關閉**"。
我們建議您在所有應用上啟用快照調試器，以方便應用程式異常的診斷。

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本

對於 Azure 應用服務，可以在 Azure 資源管理器範本中設置應用設置，以啟用快照調試器和探測器。 添加包含應用設置作為網站的子資源的配置資源：

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

- 生成應用程式可觸發異常的流量。 然後，等待 10 到 15 分鐘才能將快照發送到應用程式見解實例。
- 請參閱 Azure 門戶中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 有關解決快照調試器問題的疑難排解，請參閱[快照調試器故障排除](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

