---
title: 針對 Synapse Studio 進行疑難排解
description: 針對 Azure Synapse Studio 進行疑難排解
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: a96709c7538bda6d6b338c29dc512902e9ca2c87
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445844"
---
# <a name="azure-synapse-studio-troubleshooting"></a>Azure Synapse Studio 疑難排解

此疑難排解指南提供有關在網路連線問題上開啟支援票證時所要提供之資訊的指示。 有了適當的資訊，我們可以更快速地解決問題。

## <a name="serverless-sql-pool-service-connectivity-issue"></a>無伺服器 SQL 集區服務連線能力問題

### <a name="symptom-1"></a>徵兆1

[連接到] 下拉式清單中的 [無伺服器 SQL 集區] 選項呈現灰色。

![symptom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>徵兆2

使用「無伺服器 SQL 集區」執行查詢可讓您「無法建立與伺服器的連線」錯誤訊息。

![徵兆2](media/troubleshooting-synapse-studio/symptom2.png)
 

## <a name="troubleshooting-steps"></a>疑難排解步驟

> [!NOTE] 
>    下列疑難排解步驟適用于 Chromium Edge 和 Chrome。 您可以使用其他瀏覽器 (例如 FireFox) 和相同的疑難排解步驟，但 [開發人員工具] 視窗可能會有與此 TSG 中的螢幕擷取畫面不同的版面配置。 可能的話，請勿使用傳統 Edge 進行疑難排解，因為在某些情況下可能會顯示不正確的資訊。

開啟 [診斷資訊] 面板，然後選取 [下載診斷] 按鈕。 保留下載的錯誤報表資訊。 您可以改為複製「會話識別碼」，並在開啟支援票證時附加該識別碼。

![診斷資訊](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

若要開始進行疑難排解，請重試您在 Azure Synapse Studio 中執行的作業。

- 針對徵兆1，請選取 [SQL 腳本] 索引標籤中 [使用資料庫] 下拉式清單右邊的 [重新整理] 按鈕，並檢查您是否可以看到 [無伺服器 SQL 集區]。
- 針對徵兆2，請試著再次執行查詢，以查看它是否能順利執行。

如果問題仍然存在，請在您的瀏覽器中按 F12 以開啟 [開發人員工具] (DevTools) 。

在 [開發人員工具] 視窗中，切換至 [網路] 面板。 如有必要，請選取 [網路] 面板工具列上的 [清除] 按鈕。
請確定已核取 [網路] 面板中的 [停用快取]。

請重試您在 Azure Synapse Studio 中執行的作業。 您可能會在 [開發人員工具] 中看到 [網路] 清單中顯示的新專案。 請注意目前的系統時間，以提供支援票證。

![網路-面板1](media/troubleshooting-synapse-studio/network-panel.png)

尋找 Url 資料行符合下列模式的專案：

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

其中 [*A*] 是您的工作區名稱，而 "-ondemand" 可以是 "-sqlod"，其中 [*B*] 應該是資料庫名稱，例如 "master"。 最多隻能有兩個專案具有相同的 URL 值，但有不同的方法值;選項和貼文。 檢查 [狀態] 資料行下的這兩個專案是否有 "200" 或 "20 倍"，其中 "x" 可以是任何單一位數。

如果其中一個專案有 "20 倍" 以外的專案，而且：

- 狀態開頭為「 (失敗) 」，請加寬 [狀態] 資料行，或將指標停留在狀態文字上方以查看完整文字。 開啟支援票證時，請包含文字和/或螢幕擷取畫面。

    ![狀態文字](media/troubleshooting-synapse-studio/status-text.png)

    - 如果您看到 ERR_NAME_NOT_RESOLVED，並在10分鐘內建立了工作區，請等候10分鐘，然後再試一次，以查看問題是否仍然存在。
    - 如果您看到 ERR_INTERNET_DISCONNECTED 或 ERR_NETWORK_CHANGED，可能表示您的電腦網路連線有問題。 請檢查您的網路連線，然後再次嘗試操作。
    - 如果您看到 ERR_CONNECTION_RESET、ERR_SSL_PROTOCOL_ERROR 或其他包含 "SSL" 的錯誤碼，則可能表示您的本機 SSL 設定有問題，或您的網路系統管理員已封鎖無伺服器 SQL 集區伺服器的存取。 開啟支援票證，並在 [描述] 中附加錯誤碼。
    - 如果您看到 ERR_NETWORK_ACCESS_DENIED，您可能需要向系統管理員確認您的本機防火牆原則是否封鎖了 *. database.windows.net 網域或遠端埠1443的存取。
    - （選擇性）在不同的電腦和（或）網路環境上，立即嘗試相同的操作，以排除電腦上的網路設定問題。

- 狀態是 "40x"、"50 倍" 或其他數位，請選取 (s) 的專案，以查看詳細資料。 您應該會看到右邊的專案詳細資料。 尋找「回應標頭」一節;然後檢查名為「存取控制-允許來源」的專案是否存在。 如果是的話，請檢查它是否具有下列其中一個值：

    - `*` (單一星號) 
    - https://web.azuresynapse.net/ 瀏覽器位址列中文字開頭的 (或其他值) 

如果回應標頭包含上述其中一個值，則表示我們應該已收集失敗資訊。 您可以視需要開啟支援票證，並選擇性地附加專案詳細資料的螢幕擷取畫面。

如果您看不到標頭，或標頭沒有上面所列的其中一個值，請在開啟票證時附加專案詳細資料的螢幕擷取畫面。

 
![專案詳細資料](media/troubleshooting-synapse-studio/item-details.png)
 
如果上述步驟無法解決您的問題，您可能需要開啟支援票證。 提交您的支援票證時，請包含本指南開頭所下載的「會話識別碼」或「診斷資訊」。

回報問題時，您可以選擇在 [開發人員工具] 中取得 [主控台] 索引標籤的螢幕擷取畫面，並將其附加至支援票證。 滾動內容，並視需要使用多個螢幕擷取畫面來捕捉整個訊息。

![開發人員工具主控台](media/troubleshooting-synapse-studio/developer-tool-console.png)

如果您要連接螢幕擷取畫面，請在拍攝螢幕擷取畫面時，提供時間 (或預估時間範圍) 。 這可協助我們查看問題。

某些瀏覽器支援在 [主控台] 索引標籤中顯示時間戳記。針對 Chromium Edge/Chrome，請開啟 [開發人員工具] 中的 [設定] 對話方塊，然後在 [喜好設定] 索引標籤中選取 [顯示時間戳記]。

![開發人員工具主控台設定](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![顯示時間戳記](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>後續步驟
如果先前的步驟無法協助您解決問題，請 [建立支援票證](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
