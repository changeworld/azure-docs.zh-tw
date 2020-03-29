---
title: 捕獲瀏覽器跟蹤以進行故障排除 |微軟文檔
description: 從瀏覽器跟蹤捕獲網路資訊，以説明解決 Azure 門戶的問題。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310691"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>擷取瀏覽器追蹤以進行疑難排解

如果要對 Azure 門戶的問題進行故障排除，並且需要聯繫 Microsoft 支援人員，我們建議您首先捕獲瀏覽器跟蹤和一些附加資訊。 您收集的資訊可以在問題發生時提供有關門戶的重要詳細資訊。 請按照本文中有關您使用的瀏覽器中的開發人員工具的步驟：Google Chrome 或 Microsoft 邊緣（鉻）、微軟邊緣（EdgeHTML）或 Apple Safari。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>谷歌鉻和微軟邊緣（鉻）

谷歌鉻和微軟邊緣（鉻）都基於[鉻開源專案](https://www.chromium.org/Home)。 以下步驟演示如何使用開發人員工具，這些工具在兩個瀏覽器中非常相似。 有關詳細資訊，請參閱[Chrome 開發工具和](https://developers.google.com/web/tools/chrome-devtools)[微軟邊緣（鉻）開發人員工具](/microsoft-edge/devtools-guide-chromium)。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在開始跟蹤_之前_登錄非常重要，以便跟蹤不包含與您的登錄相關的敏感資訊。 

1. 使用[步驟記錄器](https://support.microsoft.com/help/22878/windows-10-record-steps)開始記錄您在門戶中執行的步驟。

1. 在門戶中，導航到問題發生之前的步驟。

1. 按 F12![或選擇瀏覽器設置的](media/capture-browser-trace/chromium-icon-settings.png) > 螢幕截圖圖示**更多工具** > **開發人員工具**。

1. 預設情況下，瀏覽器僅保留當前載入的頁面的跟蹤資訊。 設置以下選項，以便瀏覽器保留所有跟蹤資訊，即使您的 repro 需要訪問多個頁面：

    1. 選擇"**網路**"選項卡，然後選擇 **"保留日誌**"。

          !["保留日誌"的螢幕截圖](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. 選擇 **"主控台"** 選項卡，選擇 **"主控台設置**"，然後選擇 **"保留日誌**"。 再次選擇 **"主控台"設置**以關閉設置窗格。

          !["保留日誌"的螢幕截圖](media/capture-browser-trace/chromium-console-preserve-log.png)

1. 選擇"**網路**"選項卡，然後選擇 **"停止錄製網路日誌"** 和 **"清除**"。

    !["停止錄製網路日誌"和"清除"的螢幕截圖](media/capture-browser-trace/chromium-stop-clear-session.png)

1. 選擇 **"記錄網路日誌**"，然後在門戶中重現問題。

    !["開始分析會話"的螢幕截圖](media/capture-browser-trace/chromium-start-session.png)

    您將看到類似于下圖的會話輸出。

    ![瀏覽器跟蹤結果的螢幕截圖](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 複製意外門戶行為後，選擇 **"停止錄製網路日誌**"，然後選擇 **"匯出 HAR**並保存檔"。

    !["出口 HAR"的螢幕截圖](media/capture-browser-trace/chromium-network-export-har.png)

1. 停止步驟記錄器，並保存檔。

1. 返回瀏覽器開發人員工具窗格中，選擇 **"主控台**"選項卡。按右鍵，然後選擇"**保存為..."，** 然後將主控台輸出保存到文字檔中。

    ![主控台輸出的螢幕截圖](media/capture-browser-trace/chromium-console-select.png)

1. 以壓縮格式（如 .zip）打包 HAR 檔、主控台輸出和螢幕錄製，並在 Microsoft 支援下共用該檔。

## <a name="microsoft-edge-edgehtml"></a>微軟邊緣（邊緣HTML）

以下步驟演示如何在 Microsoft 邊緣 （EdgeHTML） 中使用開發人員工具。 有關詳細資訊，請參閱[微軟邊緣 （EdgeHTML） 開發人員工具](/microsoft-edge/devtools-guide)。

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在開始跟蹤_之前_登錄非常重要，以便跟蹤不包含與您的登錄相關的敏感資訊。 

1. 使用[步驟記錄器](https://support.microsoft.com/help/22878/windows-10-record-steps)開始記錄您在門戶中執行的步驟。

1. 在門戶中，導航到問題發生之前的步驟。

1. 按 F12![或選擇瀏覽器設置的](media/capture-browser-trace/edge-icon-settings.png) > 螢幕截圖圖示**更多工具** > **開發人員工具**。

1. 預設情況下，瀏覽器僅保留當前載入的頁面的跟蹤資訊。 設置以下選項，以便瀏覽器保留所有跟蹤資訊，即使您的 repro 需要訪問多個頁面：

    1. 選擇"**網路"** 選項卡，然後清除 **"導航"上清除條目**的選項。

          !["導航上的清除條目"的螢幕截圖](media/capture-browser-trace/edge-network-clear-entries.png)

    1. 選擇 **"主控台"** 選項卡，然後選擇 **"保留日誌**"。

          !["保留日誌"的螢幕截圖](media/capture-browser-trace/edge-console-preserve-log.png)

1. 選擇 **"網路**"選項卡，然後選擇 **"停止分析會話**"和 **"清除會話**"。

    !["停止分析會話"和"清除會話"的螢幕截圖](media/capture-browser-trace/edge-stop-clear-session.png)

1. 選擇 **"開始分析會話**"，然後在門戶中重現該問題。

    !["開始分析會話"的螢幕截圖](media/capture-browser-trace/edge-start-session.png)

    您將看到類似于下圖的會話輸出。

    ![瀏覽器跟蹤結果的螢幕截圖](media/capture-browser-trace/edge-browser-trace-results.png)

1. 複製意外門戶行為後，選擇 **"停止分析會話**"，然後選擇 **"匯出為 HAR**並保存檔"。

    !["作為 HAR 出口"的螢幕截圖](media/capture-browser-trace/edge-network-export-har.png)

1. 停止步驟記錄器，並保存檔。

1. 返回瀏覽器開發人員工具窗格，選擇 **"主控台"** 選項卡，然後展開視窗。 將游標放在主控台輸出的開頭，然後拖動並選擇輸出的全部內容。 按右鍵，然後選擇 **"複製**"，並將主控台輸出保存到文字檔中。

    ![主控台輸出的螢幕截圖](media/capture-browser-trace/edge-console-select.png)

1. 以壓縮格式（如 .zip）打包 HAR 檔、主控台輸出和螢幕錄製，並在 Microsoft 支援下共用該檔。

## <a name="apple-safari"></a>Apple Safari

以下步驟演示如何在 Apple Safari 中使用開發人員工具。 有關詳細資訊，請參閱[Safari 開發人員工具概述](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)。

1. 在 Apple Safari 中啟用開發人員工具：

    1. 選擇**Safari**，然後選擇 **"首選項**"。

        ![Safari 首選項的螢幕截圖](media/capture-browser-trace/safari-preferences.png)

    1. 選擇 **"高級**"選項卡，然後在**功能表列中選擇"顯示開發"功能表**。

        ![Safari高級首選項的螢幕截圖](media/capture-browser-trace/safari-show-develop-menu.png)

1. 登錄到 Azure[門戶](https://portal.azure.com)。 在開始跟蹤_之前_登錄非常重要，以便跟蹤不包含與您的登錄相關的敏感資訊。 

1. 開始記錄您在門戶中執行的步驟。 有關詳細資訊，請參閱如何在[Mac 上錄製螢幕](https://support.apple.com/HT208721)。

1. 在門戶中，導航到問題發生之前的步驟。

1. 選擇 **"開發**"，然後選擇 **"顯示 Web 檢查器**"。

    !["顯示 Web 檢查器"的螢幕截圖](media/capture-browser-trace/safari-show-web-inspector.png)

1. 預設情況下，瀏覽器僅保留當前載入的頁面的跟蹤資訊。 設置以下選項，以便瀏覽器保留所有跟蹤資訊，即使您的 repro 需要訪問多個頁面：

    1. 選擇"**網路**"選項卡，然後選擇 **"保留日誌**"。

          !["保留日誌"的螢幕截圖](media/capture-browser-trace/safari-network-preserve-log.png)

    1. 選擇 **"主控台"** 選項卡，然後選擇 **"保留日誌**"。

          !["保留日誌"的螢幕截圖](media/capture-browser-trace/safari-console-preserve-log.png)

1. 選擇"**網路**"選項卡，然後選擇 **"清除網路專案**"。

    !["清除網路專案"的螢幕截圖](media/capture-browser-trace/safari-clear-session.png)

1. 在門戶中重現問題。 您將看到類似于下圖的會話輸出。

    ![瀏覽器跟蹤結果的螢幕截圖](media/capture-browser-trace/safari-browser-trace-results.png)

1. 複製意外的門戶行為後，選擇 **"匯出並**保存檔"。

    !["匯出"螢幕截圖](media/capture-browser-trace/safari-network-export-har.png)

1. 停止螢幕記錄器，並保存檔。

1. 返回瀏覽器開發人員工具窗格，選擇 **"主控台"** 選項卡，然後展開視窗。 將游標放在主控台輸出的開頭，然後拖動並選擇輸出的全部內容。 使用 Command-C 複製輸出並將其保存到文字檔中。

    ![主控台輸出的螢幕截圖](media/capture-browser-trace/safari-console-select.png)

1. 以壓縮格式（如 .zip）打包 HAR 檔、主控台輸出和螢幕錄製，並在 Microsoft 支援下共用該檔。

## <a name="next-steps"></a>後續步驟

[Azure 入口網站概觀](azure-portal-overview.md)