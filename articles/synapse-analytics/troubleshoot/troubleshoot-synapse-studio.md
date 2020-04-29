---
title: 針對 Azure Synapse Studio （預覽）進行疑難排解
description: 針對 Azure Synapse Studio 進行疑難排解
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431315"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure Synapse Studio （預覽）疑難排解

此疑難排解指南提供在網路連線問題上開啟支援票證時，所要提供之資訊的指示。 有了適當的資訊，我們可以更快速地解決問題。

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL 隨選（預覽）服務連線問題

### <a name="symptom-1"></a>徵兆1

[連接到] 下拉式清單中的 [隨選 SQL] 選項呈現灰色。

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>徵兆2

以「隨選 SQL」執行查詢，會提供「無法建立與伺服器的連接」錯誤訊息。

![symptom2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>疑難排解步驟

> [!NOTE] 
>    下列疑難排解步驟適用于 Chromium Edge 和 Chrome。 您可以使用其他瀏覽器（例如 FireFox）進行相同的疑難排解步驟，但是「開發人員工具」視窗可能會與此 TSG 中的螢幕擷取畫面有不同的版面配置。 可能的話，請勿使用傳統邊緣進行疑難排解，因為在某些情況下可能會顯示不正確的資訊。

開啟 [診斷資訊] 面板，選取 [下載診斷] 按鈕。 保留下載的資訊以取得錯誤報表。 您可以改為複製 [會話識別碼]，並在開啟支援票證時附加。

![診斷資訊](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

若要開始進行疑難排解，請重試您在 Azure Synapse Studio 中執行的操作。

- 在 [徵兆 1] 中，選取 [SQL 腳本] 索引標籤中 [使用資料庫] 下拉式清單右邊的 [重新整理] 按鈕，並檢查您是否可以看到 [隨選 SQL]。
- 針對 [徵兆 2]，請嘗試再次執行查詢，以查看它是否成功執行。

如果問題仍然存在，請在瀏覽器中按 F12 以開啟「開發人員工具」（DevTools）。

在 [開發人員工具] 視窗中，切換至 [網路] 面板。 必要時，請在 [網路] 面板的工具列上選取 [清除] 按鈕。
請確定已核取 [網路] 面板中的 [停用快取]。

請重試您在 Azure Synapse Studio 中執行的操作。 您可能會在 [開發人員工具] 的 [網路] 清單中看到顯示的新專案。 請注意您目前的系統時間，以提供支援票證。

![網路-面板](media/troubleshooting-synapse-studio/network-panel.png)

尋找其 Url 資料行符合下列模式的專案：

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

其中 [*A*] 是您的工作區名稱，而 "-ondemand" 可以是 "-sqlod"，其中 [*B*] 應該是資料庫名稱，例如 "master"。 最多隻能有兩個專案具有相同的 URL 值，但有不同的方法值;選項和 POST。 檢查這兩個專案在 [狀態] 資料行下是否有 "200" 或 "20 倍"，其中 "x" 可以是任何單一數位。

如果其中一個專案具有 "20 倍" 以外的內容，而且：

- 狀態開頭為「（失敗）」，會擴大「狀態」資料行，或將指標停留在狀態文字上方，以查看完整的文字。 開啟支援票證時，請包含文字及/或螢幕擷取畫面。

    ![狀態-文字](media/troubleshooting-synapse-studio/status-text.png)

    - 如果您看到 ERR_NAME_NOT_RESOLVED，而且您在10分鐘內建立了工作區，請等候10分鐘，然後重試以查看問題是否仍然存在。
    - 如果您看到 ERR_INTERNET_DISCONNECTED 或 ERR_NETWORK_CHANGED，可能表示您的電腦網路連線發生問題。 請檢查您的網路連線，然後再次嘗試操作。
    - 如果您看到 ERR_CONNECTION_RESET、ERR_SSL_PROTOCOL_ERROR 或包含 "SSL" 的其他錯誤碼，可能表示您的本機 SSL 設定發生問題，或您的網路系統管理員已封鎖存取 SQL 隨選伺服器。 開啟支援票證，並在 [描述] 中附加錯誤碼。
    - 如果您看到 ERR_NETWORK_ACCESS_DENIED，您可能需要與系統管理員確認您的本機防火牆原則是否已封鎖存取 *. database.windows.net 網域或遠端埠1443。
    - （選擇性）在不同的電腦和/或網路環境上立即嘗試相同的操作，以排除電腦上的網路設定問題。

- 狀態為 "40x"、"50 倍" 或其他數位，請選取專案上的以查看詳細資料。 您應該會在右側看到專案詳細資料。 尋找 [回應標頭] 區段;然後檢查名為「存取控制-允許來源」的專案是否存在。 若是如此，請檢查它是否具有下列其中一個值：

    - `*`（單一星號）
    - https://web.azuresynapse.net/（或您的瀏覽器網址列中的文字開頭的其他值）

如果回應標頭包含上述其中一個值，則表示我們應該已收集失敗資訊。 如有需要，您可以開啟支援票證，並選擇性地附加專案詳細資料的螢幕擷取畫面。

如果您看不到標頭，或標頭沒有上面列出的其中一個值，請在您開啟票證時附加專案詳細資料的螢幕擷取畫面。

![專案-詳細資料](media/troubleshooting-synapse-studio/item-details.png)

如果上述步驟無法解決您的問題，您可能需要開啟支援票證。 提交您的支援票證時，請在本指南的開頭包含「會話識別碼」或「診斷資訊」。

回報問題時，您可以選擇性地在「開發人員工具」中，使用 [主控台] 索引標籤的螢幕擷取畫面，並將其附加至支援票證。 如有需要，請滾動內容並接受多個螢幕擷取畫面來捕捉整個訊息。

![開發人員工具-主控台](media/troubleshooting-synapse-studio/developer-tool-console.png)

如果您要附加螢幕擷取畫面，請提供拍攝螢幕擷取畫面時的時間（或估計的時間範圍）。 這可協助我們查看問題。

某些瀏覽器支援在 [主控台] 索引標籤中顯示時間戳記。若是 Chromium Edge/Chrome，請在 [開發人員工具] 中開啟 [設定] 對話方塊，然後在 [喜好設定] 索引標籤中選取 [顯示時間戳記]。

![開發人員工具-主控台-設定](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![顯示時間戳記](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>後續步驟
如果先前的步驟沒有助您解決問題，請[建立支援票證](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
