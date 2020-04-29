---
title: 捕捉瀏覽器追蹤以進行疑難排解 |Microsoft Docs
description: 從瀏覽器追蹤捕捉網路資訊，以協助疑難排解 Azure 入口網站的問題。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76310691"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>擷取瀏覽器追蹤以進行疑難排解

如果您要疑難排解 Azure 入口網站的問題，而且需要洽詢 Microsoft 支援服務，建議您先取得瀏覽器追蹤和一些額外的資訊。 您所收集的資訊可在問題發生時，提供有關入口網站的重要詳細資料。 針對您使用的瀏覽器中的開發人員工具，遵循本文中的步驟： Google Chrome 或 Microsoft Edge （Chromium）、Microsoft Edge （EdgeHTML）或 Apple Safari。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 和 Microsoft Edge （Chromium）

Google Chrome 和 Microsoft Edge （Chromium）都是以[Chromium 開放原始碼專案](https://www.chromium.org/Home)為基礎。 下列步驟示範如何使用開發人員工具，這在這兩個瀏覽器中非常類似。 如需詳細資訊，請參閱[Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools)和[Microsoft Edge （Chromium）開發人員工具](/microsoft-edge/devtools-guide-chromium)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必先登入，_再_開始追蹤，使追蹤不會包含與您的登入相關的機密資訊。 

1. 使用[步驟錄製](https://support.microsoft.com/help/22878/windows-10-record-steps)器，開始在入口網站中錄製您所採取的步驟。

1. 在入口網站中，流覽至發生問題之處的步驟。

1. 按 F12 鍵或![選取瀏覽器設定圖示](media/capture-browser-trace/chromium-icon-settings.png) > **更多工具** > **開發人員工具**的螢幕擷取畫面。

1. 根據預設，瀏覽器只會針對目前載入的頁面保留追蹤資訊。 設定下列選項，讓瀏覽器保留所有追蹤資訊，即使您的重新產生需要前往一個以上的頁面：

    1. 選取 [**網路**] 索引標籤，然後選取 [**保留記錄**]。

          ![「保留記錄」的螢幕擷取畫面](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. 選取 [**主控台**] 索引標籤，選取 [**主控台設定**]，然後選取 [**保留記錄**]。 再次選取 [**主控台設定**]，以關閉 [設定] 窗格。

          ![「保留記錄」的螢幕擷取畫面](media/capture-browser-trace/chromium-console-preserve-log.png)

1. 選取 [**網路**] 索引標籤，然後選取 [**停止錄製網路記錄**檔] 和 [**清除**]。

    ![「停止錄製網路記錄檔」和「清除」的螢幕擷取畫面](media/capture-browser-trace/chromium-stop-clear-session.png)

1. 選取 [**記錄網路**記錄]，然後在入口網站中重現問題。

    ![「啟動分析會話」的螢幕擷取畫面](media/capture-browser-trace/chromium-start-session.png)

    您會看到類似下圖的會話輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [**停止錄製網路記錄**]，然後選取 [**匯出 HAR** ] 並儲存檔案。

    ![「匯出 HAR」的螢幕擷取畫面](media/capture-browser-trace/chromium-network-export-har.png)

1. 停止步驟錄製器，並儲存檔案。

1. 回到瀏覽器開發人員工具窗格，選取 [**主控台**] 索引標籤。按一下滑鼠右鍵，然後選取 [**另存**新檔 ...]，然後將主控台輸出儲存到文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/chromium-console-select.png)

1. 以壓縮格式（例如 .zip）封裝 HAR 檔案、主控台輸出和螢幕錄製，並與 Microsoft 支援服務共用。

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge （EdgeHTML）

下列步驟示範如何使用 Microsoft Edge 中的開發人員工具（EdgeHTML）。 如需詳細資訊，請參閱[Microsoft Edge （EdgeHTML）開發人員工具](/microsoft-edge/devtools-guide)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必先登入，_再_開始追蹤，使追蹤不會包含與您的登入相關的機密資訊。 

1. 使用[步驟錄製](https://support.microsoft.com/help/22878/windows-10-record-steps)器，開始在入口網站中錄製您所採取的步驟。

1. 在入口網站中，流覽至發生問題之處的步驟。

1. 按 F12 鍵或![選取瀏覽器設定圖示](media/capture-browser-trace/edge-icon-settings.png) > **更多工具** > **開發人員工具**的螢幕擷取畫面。

1. 根據預設，瀏覽器只會針對目前載入的頁面保留追蹤資訊。 設定下列選項，讓瀏覽器保留所有追蹤資訊，即使您的重新產生需要前往一個以上的頁面：

    1. 選取 [**網路**] 索引標籤，然後清除 [**流覽時清除專案**] 選項。

          ![「流覽時清除專案」的螢幕擷取畫面](media/capture-browser-trace/edge-network-clear-entries.png)

    1. 選取 [**主控台**] 索引標籤，然後選取 [**保留記錄**]。

          ![「保留記錄」的螢幕擷取畫面](media/capture-browser-trace/edge-console-preserve-log.png)

1. 選取 [**網路**] 索引標籤，然後選取 [**停止分析會話**] 和 [**清除會話**]。

    ![「停止分析會話」和「清除會話」的螢幕擷取畫面](media/capture-browser-trace/edge-stop-clear-session.png)

1. 選取 [**開始分析會話]**，然後在入口網站中重現問題。

    ![「啟動分析會話」的螢幕擷取畫面](media/capture-browser-trace/edge-start-session.png)

    您會看到類似下圖的會話輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/edge-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [**停止分析會話]**，然後選取 [**匯出為 HAR** ] 並儲存檔案。

    ![「匯出為 HAR」的螢幕擷取畫面](media/capture-browser-trace/edge-network-export-har.png)

1. 停止步驟錄製器，並儲存檔案。

1. 回到 [瀏覽器開發人員工具] 窗格，選取 [**主控台**] 索引標籤，然後展開視窗。 將游標放在主控台輸出的開頭，然後拖曳並選取輸出的完整內容。 以滑鼠右鍵按一下，然後選取 [**複製**]，然後將主控台輸出儲存至文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/edge-console-select.png)

1. 以壓縮格式（例如 .zip）封裝 HAR 檔案、主控台輸出和螢幕錄製，並與 Microsoft 支援服務共用。

## <a name="apple-safari"></a>Apple Safari

下列步驟示範如何使用 Apple Safari 中的開發人員工具。 如需詳細資訊，請參閱[Safari 開發人員工具總覽](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)。

1. 啟用 Apple Safari 中的開發人員工具：

    1. 選取 [ **Safari**]，然後選取 [**喜好**設定]。

        ![Safari 喜好設定的螢幕擷取畫面](media/capture-browser-trace/safari-preferences.png)

    1. 選取 [ **Advanced** ] 索引標籤，然後選取**功能表列中的 [顯示開發] 功能表**。

        ![Safari advanced 喜好設定的螢幕擷取畫面](media/capture-browser-trace/safari-show-develop-menu.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必先登入，_再_開始追蹤，使追蹤不會包含與您的登入相關的機密資訊。 

1. 開始錄製您在入口網站中所採取的步驟。 如需詳細資訊，請參閱[如何在 Mac 上錄製螢幕](https://support.apple.com/HT208721)。

1. 在入口網站中，流覽至發生問題之處的步驟。

1. 選取 [**開發**]，然後選取 [**顯示 Web Inspector**]。

    ![「顯示 Web 偵測器」的螢幕擷取畫面](media/capture-browser-trace/safari-show-web-inspector.png)

1. 根據預設，瀏覽器只會針對目前載入的頁面保留追蹤資訊。 設定下列選項，讓瀏覽器保留所有追蹤資訊，即使您的重新產生需要前往一個以上的頁面：

    1. 選取 [**網路**] 索引標籤，然後選取 [**保留記錄**]。

          ![「保留記錄」的螢幕擷取畫面](media/capture-browser-trace/safari-network-preserve-log.png)

    1. 選取 [**主控台**] 索引標籤，然後選取 [**保留記錄**]。

          ![「保留記錄」的螢幕擷取畫面](media/capture-browser-trace/safari-console-preserve-log.png)

1. 選取 [**網路**] 索引標籤，然後選取 [**清除網路專案**]。

    ![「清除網路專案」的螢幕擷取畫面](media/capture-browser-trace/safari-clear-session.png)

1. 請在入口網站中重現問題。 您會看到類似下圖的會話輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/safari-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [**匯出**] 並儲存檔案。

    ![「匯出」的螢幕擷取畫面](media/capture-browser-trace/safari-network-export-har.png)

1. 停止螢幕錄製器並儲存檔案。

1. 回到 [瀏覽器開發人員工具] 窗格，選取 [**主控台**] 索引標籤，然後展開視窗。 將游標放在主控台輸出的開頭，然後拖曳並選取輸出的完整內容。 使用命令-C 來複製輸出，並將其儲存至文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/safari-console-select.png)

1. 以壓縮格式（例如 .zip）封裝 HAR 檔案、主控台輸出和螢幕錄製，並與 Microsoft 支援服務共用。

## <a name="next-steps"></a>後續步驟

[Azure 入口網站概觀](azure-portal-overview.md)