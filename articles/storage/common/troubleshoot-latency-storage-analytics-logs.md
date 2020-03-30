---
title: 使用儲存體分析記錄排除延遲問題
description: 使用 Azure 存儲分析日誌識別和解決延遲問題，並優化用戶端應用程式。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196512"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>使用儲存體分析記錄排除延遲問題

診斷和故障排除是使用 Azure 存儲構建和支援用戶端應用程式的關鍵技能。

由於 Azure 應用程式的分散式特性，診斷和排除錯誤和性能問題可能比傳統環境中更複雜。

以下步驟演示如何使用 Azure 存儲分析日誌識別和解決延遲問題，並優化用戶端應用程式。

## <a name="recommended-steps"></a>建議的步驟

1. 下載[存儲分析日誌](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)。

2. 使用以下 PowerShell 腳本將原始格式日誌轉換為表格格式：

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. 腳本將啟動一個 GUI 視窗，您可以在其中按列篩選資訊，如下所示。

   ![存儲分析日誌解析器視窗](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. 根據"操作類型"縮小日誌條目範圍，並查找在問題時間範圍內創建的日誌條目。

   ![操作類型日誌條目](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 在出現問題期間，以下值很重要：

   * 操作類型 = 獲取Blob
   * 請求狀態 = SAS網路錯誤
   * 端到端延遲-內向延遲 = 8453
   * 伺服器延遲-在內 = 391

   使用以下公式計算端到端延遲：

   * 端到端延遲 + 伺服器延遲 + 用戶端延遲

   使用日誌條目計算用戶端延遲：

   * 用戶端延遲 = 端到端延遲 + 伺服器延遲

          * Example: 8453 – 391 = 8062ms

   下表提供有關高延遲操作類型和請求狀態結果的資訊：

   |   |請求狀態*<br>Success|請求狀態*<br>（SAS）網路錯誤|建議|
   |---|---|---|---|
   |GetBlob|是|否|[**獲取Blob操作：** 請求狀態 = 成功](#getblob-operation-requeststatus--success)|
   |GetBlob|否|是|[**獲取Blob操作：** 請求狀態 = （SAS） 網路錯誤](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|是|否|[**放置操作：** 請求狀態 = 成功](#put-operation-requeststatus--success)|
   |PutBlob|否|是|[**放置操作：** 請求狀態 = （SAS） 網路錯誤](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>狀態結果

### <a name="getblob-operation-requeststatus--success"></a>獲取 Blob 操作：請求狀態 = 成功

檢查"建議步驟"部分步驟 5 中提到的以下值：

* 端到端延遲
* 伺服器延遲
* 用戶端延遲

在具有**請求狀態 = 成功** **GetBlob 操作**中，如果在**用戶端延遲**中花費**最大時間**，則表示 Azure 存儲花費大量時間向用戶端寫入資料。 此延遲表示用戶端問題。

**建議：**

* 調查用戶端中的代碼。
* 使用 Wireshark、Microsoft 消息分析器或 Tcping 調查來自用戶端的網路連接問題。 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>獲取Blob操作：請求狀態 = （SAS）網路錯誤

檢查"建議步驟"部分步驟 5 中提到的以下值：

* 端到端延遲
* 伺服器延遲
* 用戶端延遲

在具有請求狀態的**GetBlob 操作****中 = （SAS） 網路錯誤**中，如果在**用戶端延遲**中花費**最大時間**，最常見的問題是用戶端在存儲服務中超時到期之前斷開連接。

**建議：**

* 請調查用戶端裡的程式碼，了解用戶端何時及為何與儲存體服務中斷連線。
* 使用 Wireshark、Microsoft 消息分析器或 Tcping 調查來自用戶端的網路連接問題。 

### <a name="put-operation-requeststatus--success"></a>放置操作：請求狀態 = 成功

檢查"建議步驟"部分步驟 5 中提到的以下值：

* 端到端延遲
* 伺服器延遲
* 用戶端延遲

在"具有**請求狀態的****放置操作**= 成功"中，如果在**用戶端延遲**中花費**最大時間**，則表示用戶端花費更多時間將資料發送到 Azure 存儲。 此延遲表示用戶端問題。

**建議：**

* 調查用戶端中的代碼。
* 使用 Wireshark、Microsoft 消息分析器或 Tcping 調查來自用戶端的網路連接問題。 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>放置操作：請求狀態 = （SAS）網路錯誤

檢查"建議步驟"部分步驟 5 中提到的以下值：

* 端到端延遲
* 伺服器延遲
* 用戶端延遲

在具有**請求狀態 （ SAS） 網路錯誤的** **PutBlob 操作**中，如果在**用戶端延遲**中花費**最大時間**，最常見的問題是用戶端在存儲服務中超時到期之前斷開連接。

**建議：**

* 請調查用戶端裡的程式碼，了解用戶端何時及為何與儲存體服務中斷連線。
* 使用 Wireshark、Microsoft 消息分析器或 Tcping 調查來自用戶端的網路連接問題。

