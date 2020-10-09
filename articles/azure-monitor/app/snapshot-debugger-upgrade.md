---
title: 升級 Azure 應用程式見解快照偵錯工具
description: 如何將 .NET 應用程式的快照偵錯工具升級到 Azure App Services 或 Nuget 套件的最新版本
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77671387"
---
# <a name="upgrading-the-snapshot-debugger"></a>升級快照偵錯工具

為了盡可能為您的資料提供最佳的安全性，Microsoft 會從 TLS 1.0 和 TLS 1.1 中移出，而這些都是很容易被判定的攻擊者所造成。 如果您使用的是較舊版本的網站延伸模組，則需要升級才能繼續工作。 本檔概述將快照偵錯工具升級至最新版本所需的步驟。 有兩個主要的升級路徑，取決於您是否使用網站擴充功能啟用快照偵錯工具，或您是否使用已加入至應用程式的 SDK/Nuget。 以下將討論這兩種升級路徑。 

## <a name="upgrading-the-site-extension"></a>升級網站延伸模組

> [!IMPORTANT]
> 舊版 Application Insights 使用稱為 _Application Insights 擴充功能的_私用網站延伸模組進行 Azure App Service。 您可以藉由將應用程式設定設為淺預先安裝的網站延伸模組，來啟用目前的 Application Insights 體驗。
> 為了避免發生衝突，可能會導致您的網站停止運作，請務必先刪除私人網站延伸模組。 請參閱下面的步驟4。

如果您使用網站擴充功能啟用快照偵錯工具，您可以使用下列程式進行升級：

1. 登入 Azure 入口網站。
2. 流覽至已啟用 Application Insights 和快照偵錯工具的資源。 例如，針對 Web 應用程式，流覽至 App Service 資源：

   ![名為 DiagService01 之個別 App Service 資源的螢幕擷取畫面](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 導覽至您的資源之後，請按一下 [延伸模組] 分頁，並等候延伸模組清單填入：

   ![App Service 擴充功能的螢幕擷取畫面，其中顯示已安裝 Azure App Service 的 Application Insights 延伸模組](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. 如果已安裝 _Azure App Service 的 Application Insights 延伸_ 模組的任何版本，請選取它，然後按一下 [刪除]。 確認 [ **是]** 可刪除擴充功能，並等候刪除完成，再移至下一個步驟。

   ![App Service 擴充功能的螢幕擷取畫面，其中顯示已醒目提示 [刪除] 按鈕的 Azure App Service Application Insights 延伸模組](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 移至資源的總覽分頁，然後按一下 [Application Insights：

   ![三個按鈕的螢幕擷取畫面。 已選取名稱為 Application Insights 的中間按鈕](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 如果這是您第一次流覽此 App Service 的 Application Insights 分頁，系統會提示您開啟 Application Insights。 選取 [ **開啟 Application Insights**]。
 
   ![顯示反白顯示 [開啟 Application Insights] 按鈕之 Application Insights 分頁的第一次體驗的螢幕擷取畫面](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 目前的 Application Insights 設定隨即顯示。 除非您想要變更設定的機會，否則您可以保持原狀。 預設不會啟用分頁底部 **的 [套用] 按鈕，您** 必須切換其中一個設定才能啟動按鈕。 您不需要變更任何實際的設定，而是可以變更設定，然後立即將它變更回來。 建議您切換 Profiler 設定， **然後選取 [** 套用]。

   ![Application Insights App Service 設定頁面的螢幕擷取畫面，其中的 [套用] 按鈕以紅色反白顯示](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. **按一下 [** 套用] 後，系統會要求您確認變更。

    > [!NOTE]
    > 此網站將會在升級過程中重新開機。

   ![App Service 的 [套用監視] 提示的螢幕擷取畫面。 文字方塊會顯示訊息：「我們現在會將變更套用至您的應用程式設定，並安裝工具，將您的 Application Insights 資源連結到 web 應用程式。 這會重新開機網站。 您要繼續嗎？」](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. 按一下 **[是]** 以套用變更，並等候進程完成。

現在已升級網站，並已可供使用。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升級快照偵錯工具

如果應用程式使用下列版本的 `Microsoft.ApplicationInsights.SnapshotCollector` 1.3.1，則必須將其升級至 [較新的版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) ，才能繼續工作。
