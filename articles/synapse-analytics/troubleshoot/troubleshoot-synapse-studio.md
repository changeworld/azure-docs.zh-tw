---
title: 排除 Azure 同步工作室(預覽)
description: 排除 Azure 突觸工作室
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431315"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure 同步工作室(預覽)故障排除

此故障排除指南提供有關在打開網路連接問題支援票證時要提供哪些資訊的說明。 有了正確的信息,我們可以更快地解決問題。

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL 按需(預覽)服務連線問題

### <a name="symptom-1"></a>症狀 1

"SQL 按需"選項在"連接到"下拉列表中顯示為灰色。

![症狀1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>症狀 2

使用「SQL 按需」執行查詢會提供「無法建立與伺服器的連接」錯誤訊息。

![症狀2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>疑難排解步驟

> [!NOTE] 
>    以下故障排除步驟適用於鉻邊緣和鉻。 您可以使用具有相同故障排除步驟的其他瀏覽器(如 FireFox),但「開發人員工具」視窗的佈局可能與此 TSG 中的螢幕截圖不同。 如果可能,請勿使用經典邊緣進行故障排除,因為它可能會在特定情況下顯示不準確的資訊。

打開「診斷資訊」面板,選擇「下載診斷」按鈕。 保留下載的資訊以進行錯誤報告。 您可以複製「工作階段 ID」並在打開支援票證時附加它。

![診斷資訊](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

要開始故障排除,請重試在 Azure 同步工作室中執行的操作。

- 對於癥狀 1,在「SQL 腳本」選項卡中選擇「使用資料庫」下拉列表右側的「刷新」按鈕,並檢查是否可以看到「SQL 按需」。
- 對於癥狀 2,請嘗試再次運行查詢以查看其是否成功執行。

如果問題仍然存在,請按瀏覽器中的 F12 打開"開發人員工具"(DevTools)。

在"開發人員工具"視窗中,切換到"網路"面板。 如有必要,選擇「網路」面板中工具列上的「清除」 按鈕。
確保選中「網路」面板中的「禁用緩存」。

重試在 Azure 同步工作室中執行的操作。 您可能會在「開發人員工具」中的「網路」清單中看到顯示的新專案。 請注意當前系統時間,以在支援票證中提供。

![網路面板](media/troubleshooting-synapse-studio/network-panel.png)

尋找網址列與以下模式符合的項目:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

其中 [*A*] 是工作區名稱,並且"按需"可以是"-sqlod",其中 [*B*] 應為資料庫名稱,如"master"。 最多應有兩個具有相同 URL 值但方法值不同的項;選項和 POST。 檢查這兩個項目在狀態列下是否有"200"或"20x",其中"x"可以是任何個位數。

如果其中任一人有「20x」以外的東西,並且:

- 狀態以"(失敗)"開頭,請加寬"狀態"列或將指標懸停在狀態文本上以查看完整文本。 在打開支援票證時包括文本和/或屏幕截圖。

    ![狀態文字](media/troubleshooting-synapse-studio/status-text.png)

    - 如果您在 10 分鐘內看到ERR_NAME_NOT_RESOLVED並創建了工作區,請等待 10 分鐘,然後重試以查看問題是否仍然存在。
    - 如果您看到ERR_INTERNET_DISCONNECTED或ERR_NETWORK_CHANGED,則可能表示您的 PC 網路連接有問題。 檢查網路連接並重試操作。
    - 如果您看到包含"SSL"的ERR_CONNECTION_RESET、ERR_SSL_PROTOCOL_ERROR或其他錯誤代碼,則可能表明本地 SSL 配置有問題,或者您的網路管理員已阻止對 SQL 按需伺服器的訪問。 打開支援票證並在說明中附加錯誤代碼。
    - 如果您看到ERR_NETWORK_ACCESS_DENIED,您可能需要與管理員聯繫,瞭解本地防火牆策略是否阻止了對 *.database.windows.net域或遠端埠 1443 的訪問。
    - 或者,請立即嘗試其他計算機和/或網路環境中的相同操作,以排除電腦上的網路配置問題。

- 狀態為"40x"、"50x"或其他號碼,在專案上選擇以查看詳細資訊。 您應該看到右側的專案詳細資訊。 查找「回應標頭」部分;然後檢查是否存在名為"訪問控制-允許源"的專案。 如果是,請檢查它是否具有以下值之一:

    - `*`(儲存星號)
    - https://web.azuresynapse.net/( 或瀏覽器位址列中的文字開頭的其他值)

如果回應標頭包含上述值之一,則意味著我們應已收集失敗資訊。 如果需要,您可以打開支援票證,並可以選擇附加專案詳細資訊的螢幕截圖。

如果看不到標頭,或者標頭沒有上面列出的值之一,請打開票證時附加項目詳細資訊的屏幕截圖。

![專案詳細資訊](media/troubleshooting-synapse-studio/item-details.png)

如果上述步驟不能解決您的問題,您可能需要打開支援票證。 提交支援票證時,請包括本指南開頭下載的"會話 ID"或"診斷資訊"。

報告問題時,您可以選擇在「開發人員工具」中截取「控制台」選項卡的螢幕截圖,並將其附加到支援票證。 如有必要,滾動內容並獲取多個螢幕截圖以捕獲整個郵件。

![開發人員工具主控台](media/troubleshooting-synapse-studio/developer-tool-console.png)

如果要附加屏幕截圖,請提供截圖的時間(或估計時間範圍)。 它將有助於我們調查這個問題。

某些瀏覽器支援在「控制台」選項卡中顯示時間戳。對於鉻邊緣/鉻,請在"開發人員工具"中打開"設置"對話框,並在"首選項"選項卡中選中"顯示時間戳"。

![開發人員-工具主控台設定](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![顯示時間戳](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>後續步驟
如果前面的步驟無助於解決您簽發的問題[創建支援票證](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
