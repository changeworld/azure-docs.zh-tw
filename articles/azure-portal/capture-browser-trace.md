---
title: 擷取瀏覽器追蹤以進行疑難排解 | Microsoft Docs
description: 從瀏覽器追蹤擷取網路資訊，以協助疑難排解 Azure 入口網站的問題。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: dba321d055e64d62ca91f95461c3299bee5f90d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83714214"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>擷取瀏覽器追蹤以進行疑難排解

如果您要針對 Azure 入口網站的問題進行疑難排解，而且需要聯絡 Microsoft 支援服務，建議您先取得瀏覽器追蹤和一些額外的資訊。 您所收集的資訊可在問題發生時，提供有關入口網站的重要詳細資料。 依據您所使用的瀏覽器，遵循各瀏覽器的開發人員工具步驟：Google Chrome 或 Microsoft Edge (Chromium)、Microsoft Edge (EdgeHTML)、Apple Safari 或 Firefox。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 和 Microsoft Edge (Chromium)

Google Chrome 和 Microsoft Edge (Chromium) 都是以 [Chromium 開放原始碼專案](https://www.chromium.org/Home)為基礎。 下列步驟示範如何使用開發人員工具，這兩個瀏覽器的做法非常類似。 如需詳細資訊，請參閱 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) 和 [Microsoft Edge (Chromium) 開發人員工具](/microsoft-edge/devtools-guide-chromium)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必「先」登入，然後再開始追蹤，這樣追蹤才不會包含與您的登入相關的敏感性資訊。 

1. 使用[步驟收錄程式](https://support.microsoft.com/help/22878/windows-10-record-steps)，開始錄製您在入口網站中所採取的步驟。

1. 在入口網站中，瀏覽至發生問題之前的步驟。

1. 按 F12 鍵或選取![瀏覽器設定圖示的螢幕擷取畫面](media/capture-browser-trace/chromium-icon-settings.png) >  [其他工具]  >  [開發人員工具]。

1. 根據預設，瀏覽器只會保留目前載入頁面的追蹤資訊。 設定下列選項，讓瀏覽器保留所有的追蹤資訊，即使您的重現需要前往一個以上的頁面：

    1. 選取 [網路] 索引標籤，然後選取 [保留記錄]。

          ![[保留記錄] 的螢幕擷取畫面](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. 選取 [主控台] 索引標籤，選取 [主控台設定]，然後選取 [保留記錄]。 再次選取 [主控台設定] 以關閉設定窗格。

          ![[保留記錄] 的螢幕擷取畫面](media/capture-browser-trace/chromium-console-preserve-log.png)

1. 選取 [網路] 索引標籤，然後選取 [停止錄製網路記錄] 和 [清除]。

    ![[停止錄製網路記錄] 和 [清除] 的螢幕擷取畫面](media/capture-browser-trace/chromium-stop-clear-session.png)

1. 選取 [錄製網路記錄]，然後在入口網站中重現問題。

    ![[啟動分析工作階段] 的螢幕擷取畫面](media/capture-browser-trace/chromium-start-session.png)

    您將看到類似下圖的工作階段輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [停止錄製網路記錄]，然後選取 [匯出 HAR] 並儲存檔案。

    ![[匯出 HAR] 的螢幕擷取畫面](media/capture-browser-trace/chromium-network-export-har.png)

1. 停止步驟收錄程式，並儲存錄製內容。

1. 回到瀏覽器開發人員工具窗格，選取 [主控台] 索引標籤。以滑鼠右鍵按一下其中一個訊息，選取 [另存新檔 ...]，然後將主控台輸出儲存至文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/chromium-console-select.png)

1. 以壓縮格式 (例如 .zip) 封裝 HAR 檔案、主控台輸出和螢幕錄製內容，然後與 Microsoft 支援服務共用。

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

下列步驟示範如何使用 Microsoft Edge (EdgeHTML) 中的開發人員工具。 如需詳細資訊，請參閱 [Microsoft Edge (EdgeHTML) 開發人員工具](/microsoft-edge/devtools-guide)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必「先」登入，然後再開始追蹤，這樣追蹤才不會包含與您的登入相關的敏感性資訊。 

1. 使用[步驟收錄程式](https://support.microsoft.com/help/22878/windows-10-record-steps)，開始錄製您在入口網站中所採取的步驟。

1. 在入口網站中，瀏覽至發生問題之前的步驟。

1. 按 F12 鍵或選取![瀏覽器設定圖示的螢幕擷取畫面](media/capture-browser-trace/edge-icon-settings.png) >  [其他工具]  >  [開發人員工具]。

1. 根據預設，瀏覽器只會保留目前載入頁面的追蹤資訊。 設定下列選項，讓瀏覽器保留所有的追蹤資訊，即使您的重現需要前往一個以上的頁面：

    1. 選取 [網路] 索引標籤，然後清除 [清除瀏覽過程中的項目] 選項。

          ![[清除瀏覽過程中的項目] 的螢幕擷取畫面](media/capture-browser-trace/edge-network-clear-entries.png)

    1. 選取 [主控台] 索引標籤，然後選取 [保留記錄]。

          ![[保留記錄] 的螢幕擷取畫面](media/capture-browser-trace/edge-console-preserve-log.png)

1. 選取 [網路] 索引標籤，然後選取 [停止分析工作階段] 和 [清除工作階段]。

    ![[停止分析工作階段] 和 [清除工作階段] 的螢幕擷取畫面](media/capture-browser-trace/edge-stop-clear-session.png)

1. 選取 [開始分析工作階段]，然後在入口網站中重現問題。

    ![[啟動分析工作階段] 的螢幕擷取畫面](media/capture-browser-trace/edge-start-session.png)

    您將看到類似下圖的工作階段輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/edge-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [停止分析工作階段]，然後選取 [匯出為 HAR] 並儲存檔案。

    ![[匯出為 HAR] 的螢幕擷取畫面](media/capture-browser-trace/edge-network-export-har.png)

1. 停止步驟收錄程式，並儲存錄製內容。

1. 回到瀏覽器開發人員工具窗格，選取 [主控台] 索引標籤並展開視窗。 將游標放在主控台輸出的開頭，然後拖曳並選取輸出的完整內容。 以滑鼠右鍵按一下，選取 [複製]，然後將主控台輸出儲存至文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/edge-console-select.png)

1. 以壓縮格式 (例如 .zip) 封裝 HAR 檔案、主控台輸出和螢幕錄製內容，然後與 Microsoft 支援服務共用。

## <a name="apple-safari"></a>Apple Safari

下列步驟示範如何使用 Apple Safari 中的開發人員工具。 如需詳細資訊，請參閱 [Safari 開發人員工具概觀](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)。

1. 啟用 Apple Safari 中的開發人員工具：

    1. 選取 [Safari]，然後選取 [偏好設定]。

        ![Safari 偏好設定的螢幕擷取畫面](media/capture-browser-trace/safari-preferences.png)

    1. 選取 [進階] 索引標籤，然後選取 [在功能表列中顯示開發功能表]。

        ![Safari 進階偏好設定的螢幕擷取畫面](media/capture-browser-trace/safari-show-develop-menu.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必「先」登入，然後再開始追蹤，這樣追蹤才不會包含與您的登入相關的敏感性資訊。 

1. 開始錄製您在入口網站中所採取的步驟。 如需詳細資訊，請參閱[如何在 Mac 上錄製螢幕](https://support.apple.com/HT208721)。

1. 在入口網站中，瀏覽至發生問題之前的步驟。

1. 選取 [開發]，然後選取 [顯示 Web 檢查程式]。

    ![[顯示 Web 檢查程式] 的螢幕擷取畫面](media/capture-browser-trace/safari-show-web-inspector.png)

1. 根據預設，瀏覽器只會保留目前載入頁面的追蹤資訊。 設定下列選項，讓瀏覽器保留所有的追蹤資訊，即使您的重現需要前往一個以上的頁面：

    1. 選取 [網路] 索引標籤，然後選取 [保留記錄]。

          ![[保留記錄] 的螢幕擷取畫面](media/capture-browser-trace/safari-network-preserve-log.png)

    1. 選取 [主控台] 索引標籤，然後選取 [保留記錄]。

          ![[保留記錄] 的螢幕擷取畫面](media/capture-browser-trace/safari-console-preserve-log.png)

1. 選取 [網路] 索引標籤，然後選取 [清除網路項目]。

    ![[清除網路項目] 的螢幕擷取畫面](media/capture-browser-trace/safari-clear-session.png)

1. 請在入口網站中重現問題。 您將看到類似下圖的工作階段輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/safari-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [匯出] 並儲存檔案。

    ![[匯出] 的螢幕擷取畫面](media/capture-browser-trace/safari-network-export-har.png)

1. 停止螢幕錄製程式，並儲存錄製內容。

1. 回到瀏覽器開發人員工具窗格，選取 [主控台] 索引標籤並展開視窗。 將游標放在主控台輸出的開頭，然後拖曳並選取輸出的完整內容。 使用 -C 命令來複製輸出，並將儲存至文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/safari-console-select.png)

1. 以壓縮格式 (例如 .zip) 封裝 HAR 檔案、主控台輸出和螢幕錄製內容，然後與 Microsoft 支援服務共用。

## <a name="firefox"></a>Firefox

下列步驟示範如何使用 Firefox 中的開發人員工具。 如需詳細資訊，請參閱 [Firefox 開發人員工具](https://developer.mozilla.org/docs/Tools)。

1. 登入 [Azure 入口網站](https://portal.azure.com)。 請務必「先」登入，然後再開始追蹤，這樣追蹤才不會包含與您的登入相關的敏感性資訊。 

1. 開始錄製您在入口網站中所採取的步驟。 在 Windows 上使用[步驟收錄程式](https://support.microsoft.com/help/22878/windows-10-record-steps)，或參閱[如何在 Mac 上錄製螢幕](https://support.apple.com/HT208721)。

1. 在入口網站中，瀏覽至發生問題之前的步驟。

1. 按 F12 鍵或選取![瀏覽器設定圖示的螢幕擷取畫面](media/capture-browser-trace/firefox-icon-settings.png) >  [Web 開發工具]  >  [切換工具]。

1. 根據預設，瀏覽器只會保留目前載入頁面的追蹤資訊。 設定下列選項，讓瀏覽器保留所有的追蹤資訊，即使您的重現需要前往一個以上的頁面：

    1. 選取 [網路] 索引標籤，然後選取 [保存記錄]。

          ![[保存記錄] 的螢幕擷取畫面](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. 選取 [主控台] 索引標籤，選取 [主控台設定]，然後選取 [保存記錄]。

          ![[保存記錄] 的螢幕擷取畫面](media/capture-browser-trace/firefox-console-persist-logs.png)

1. 選取 [網路] 索引標籤，然後選取 [清除]。

    ![[清除] 的螢幕擷取畫面。](media/capture-browser-trace/firefox-clear-session.png)

1. 請在入口網站中重現問題。 您將看到類似下圖的工作階段輸出。

    ![瀏覽器追蹤結果的螢幕擷取畫面](media/capture-browser-trace/firefox-browser-trace-results.png)

1. 重現非預期的入口網站行為之後，請選取 [HAR 匯出/匯入]，然後選取 [全部儲存為 HAR]。

    ![[匯出 HAR] 的螢幕擷取畫面](media/capture-browser-trace/firefox-network-export-har.png)

1. 停止 Windows 上的步驟收錄程式或 Mac 上的螢幕錄製，並儲存錄製內容。

1. 回到瀏覽器開發人員工具窗格，選取 [主控台] 索引標籤。以滑鼠右鍵按一下其中一個訊息，選取 [將顯示的訊息匯出至]，然後將主控台輸出儲存至文字檔。

    ![主控台輸出的螢幕擷取畫面](media/capture-browser-trace/firefox-console-select.png)

1. 以壓縮格式 (例如 .zip) 封裝 HAR 檔案、主控台輸出和螢幕錄製內容，然後與 Microsoft 支援服務共用。

## <a name="next-steps"></a>後續步驟

[Azure 入口網站概觀](azure-portal-overview.md)