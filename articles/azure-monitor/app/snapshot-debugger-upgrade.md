---
title: 升級 Azure 應用程式見解快照調試器
description: 如何將 .NET 應用的快照調試器升級到 Azure 應用服務上的最新版本，或通過 Nuget 包
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671387"
---
# <a name="upgrading-the-snapshot-debugger"></a>升級快照調試器

為了為您的資料提供盡可能好的安全性，Microsoft 正在遠離 TLS 1.0 和 TLS 1.1，TLS 1.1 已被證明容易受到堅定的攻擊者的攻擊。 如果您使用的是舊版本的網站擴展，則需要升級才能繼續工作。 本文檔概述了將快照調試器升級到最新版本所需的步驟。 有兩種主要升級路徑，具體取決於是否使用網站擴展啟用了快照調試器，或者是否使用了添加到應用程式的 SDK/Nuget。 下面將討論這兩個升級路徑。 

## <a name="upgrading-the-site-extension"></a>升級網站擴展

> [!IMPORTANT]
> 舊版本的應用程式見解使用_名為 Azure 應用服務的應用程式見解擴展_項。 通過設置應用設置以點亮預先安裝的網站擴展，啟用當前應用程式見解體驗。
> 為了避免可能導致網站停止工作的衝突，請務必首先刪除私人網路站擴展。 請參閱下面的步驟 4。

如果使用網站擴展啟用了快照調試器，則可以使用以下過程進行升級：

1. 登入 Azure 入口網站。
2. 導航到已啟用應用程式見解和快照調試器的資源。 例如，對於 Web 應用，導航到應用服務資源：

   ![名為 DiagService01 的單個應用服務資源的螢幕截圖](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. 導航到資源後，按一下"擴展"邊欄選項卡並等待擴展清單填充：

   ![應用服務擴展圖，顯示已安裝的 Azure 應用服務的應用程式見解擴展](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. 如果安裝了_Azure 應用服務的任何版本的應用程式見解擴展_，請選擇它，然後按一下"刪除"。 確認**是**刪除擴展並等待刪除完成，然後再移動到下一步。

   ![應用服務擴展圖，顯示 Azure 應用服務的應用程式見解擴展，並突出顯示"刪除"按鈕](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. 轉到資源的"概述"邊欄選項卡，然後按一下"應用程式見解"：

   ![三個按鈕的螢幕截圖。 選擇名稱"應用程式見解"的中心按鈕](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. 如果這是您第一次查看此應用服務的應用程式見解邊欄選項卡，系統將提示您打開應用程式見解。 選擇 **"打開應用程式見解**"。
 
   ![突出顯示"打開應用程式見解"按鈕的應用程式見解邊欄的首次體驗螢幕截圖](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. 將顯示當前應用程式見解設置。 除非您想借此機會更改設置，否則可以保留它們。 預設情況下，邊欄選項卡底部的 **"應用"** 按鈕未啟用，您必須切換其中一個設置才能啟動該按鈕。 您不必更改任何實際設置，而是可以更改設置，然後立即將其更改回來。 我們建議切換探測器設置，然後選擇 **"應用**"。

   ![應用程式見解應用服務配置頁面的螢幕截圖，應用按鈕以紅色突出顯示](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. 按一下 **"應用"** 後，系統將要求您確認更改。

    > [!NOTE]
    > 作為升級過程的一部分，網站將重新開機。

   ![應用服務應用監視提示的螢幕截圖。 文字方塊顯示消息："我們現在將更改應用於您的應用設置，並安裝我們的工具，將您的應用程式見解資源連結到 Web 應用。 這將重新開機網站。 您要繼續嗎？」](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. 按一下"**是**"以應用更改並等待該過程完成。

該網站現已升級，可供使用。

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>使用 SDK/Nuget 升級快照調試器

如果應用程式使用的版本`Microsoft.ApplicationInsights.SnapshotCollector`為 1.3.1，則需要將其升級到[較新版本](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)才能繼續工作。
