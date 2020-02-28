---
title: 在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具 |Microsoft Docs
description: 在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具
ms.topic: conceptual
author: brahmnes
ms.author: bfung
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: c23da585034e74d85be5a3c41b124f00408a0f4a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671421"
---
# <a name="enable-snapshot-debugger-for-net-apps-in-azure-app-service"></a>在 Azure App Service 中啟用 .NET 應用程式的快照偵錯工具

快照偵錯工具目前適用于在 Windows 服務方案上的 Azure App Service 上執行的 ASP.NET 和 ASP.NET Core 應用程式。

## <a id="installation"></a>啟用快照偵錯工具
若要啟用應用程式的快照偵錯工具，請遵循下列指示。 如果您正在執行不同類型的 Azure 服務，以下是在其他支援的平臺上啟用快照偵錯工具的指示：
* [Azure 雲端服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 服務](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure 虛擬機器和虛擬機器擴展集](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [內部部署虛擬或實體機器](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

如果您使用的是 .NET Core 的預覽版本，請先遵循針對[其他環境啟用快照偵錯工具](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)的指示，先將[microsoft.applicationinsights.snapshotcollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 套件包含在應用程式中，然後完成下列其餘指示。 

Application Insights 快照偵錯工具會預先安裝為應用程式服務執行時間的一部分，但您必須開啟它，才能取得 App Service 應用程式的快照集。 當您部署應用程式之後，即使您已在原始程式碼中包含 Application Insights SDK，請遵循下列步驟來啟用快照偵錯工具。

1. 移至 Azure 入口網站中的 [App Service] 窗格。
2. 瀏覽至 [設定] > [Application Insights] 窗格。

   ![在 App Service 入口網站上啟用 Application Insights](./media/snapshot-debugger/applicationinsights-appservices.png)

3. 依照窗格上的指示來建立新資源，或選取現有的 App Insights 資源來監視您的應用程式。 也請確定快照偵錯工具的兩個參數都是**On**。

   ![新增 App Insights 網站延伸模組][Enablement UI]

4. 快照偵錯工具現在已使用應用程式服務應用程式設定來啟用。

    ![快照偵錯工具的應用程式設定][snapshot-debugger-app-setting]

## <a name="disable-snapshot-debugger"></a>停用快照偵錯工具

遵循與 [**啟用快照偵錯工具**] 相同的步驟，但將快照偵錯工具的兩個交換器切換為 [**關閉**]。
我們建議您在所有應用程式上都啟用快照偵錯工具，以簡化應用程式例外狀況的診斷。

## <a name="next-steps"></a>後續步驟

- 產生可觸發例外狀況的應用程式流量。 然後，等候10到15分鐘，讓快照集傳送至 Application Insights 實例。
- 請參閱 Azure 入口網站中的[快照](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)集。
- 如需疑難排解快照偵錯工具問題的協助，請參閱[快照偵錯工具疑難排解](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)。

[Enablement UI]: ./media/snapshot-debugger/enablement-ui.png
[snapshot-debugger-app-setting]:./media/snapshot-debugger/snapshot-debugger-app-setting.png

