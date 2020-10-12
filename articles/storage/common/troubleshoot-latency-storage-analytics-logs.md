---
title: 使用儲存體分析記錄排除延遲問題
description: 使用 Azure 儲存體分析記錄檔，找出並針對延遲問題進行疑難排解，並將用戶端應用程式優化。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: efae9cd2a73bf6df89007ac313ca6dfe6efe6ddd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075958"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>使用儲存體分析記錄排除延遲問題

診斷和疑難排解是使用 Azure 儲存體建立和支援用戶端應用程式的重要技術。

由於 Azure 應用程式的分散本質，診斷和疑難排解錯誤和效能問題可能比傳統環境更為複雜。

下列步驟示範如何使用 Azure 儲存體分析記錄檔找出延遲問題，並對其進行疑難排解，並將用戶端應用程式優化。

## <a name="recommended-steps"></a>建議的步驟

1. 下載 [儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)檔。

2. 使用下列 PowerShell 腳本，將原始格式記錄轉換成表格格式：

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

3. 腳本將會啟動 GUI 視窗，您可以在其中依資料行篩選資訊，如下所示。

   ![儲存體分析記錄剖析器視窗](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. 根據「操作類型」縮小記錄專案的範圍，並尋找在問題的時間範圍內建立的記錄專案。

   ![操作類型記錄專案](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 發生問題時，下列值很重要：

   * Operation-type = GetBlob
   * 要求-狀態 = >sasnetworkerror
   * 端對端延遲-毫秒 = 8453
   * 伺服器-延遲-毫秒 = 391

   端對端延遲會使用下列方程式來計算：

   * 端對端延遲 = Server-Latency + 用戶端延遲

   使用記錄專案來計算用戶端延遲：

   * 用戶端延遲 = 端對端延遲– Server-Latency

        範例：8453– 391 = 8062ms

   下表提供高延遲 OperationType 和 RequestStatus 結果的相關資訊：

   | Blob 類型 |RequestStatus =<br>成功|RequestStatus =<br> (SAS) NetworkError|建議|
   |---|---|---|---|
   |GetBlob|是|否|[**GetBlob 操作：** RequestStatus = Success](#getblob-operation-requeststatus--success)|
   |GetBlob|否|是|[**GetBlob 操作：** RequestStatus = (SAS) NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|是|否|[**Put 作業：** RequestStatus = Success](#put-operation-requeststatus--success)|
   |PutBlob|否|是|[**Put 作業：** RequestStatus = (SAS) NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>狀態結果

### <a name="getblob-operation-requeststatus--success"></a>GetBlob 作業： RequestStatus = Success

檢查下列值，如「建議的步驟」一節的步驟5所述：

* 端對端延遲
* Server-Latency
* Client-Latency

在**RequestStatus = Success**的**GetBlob**作業中，如果在**用戶端延遲**時間內花費**最大時間**，這表示 Azure 儲存體正在將大量的時間寫入用戶端。 此延遲表示 Client-Side 問題。

**建議：**

* 調查用戶端中的程式碼。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 調查用戶端的網路連線問題。 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob 操作： RequestStatus = (SAS) NetworkError

檢查下列值，如「建議的步驟」一節的步驟5所述：

* 端對端延遲
* Server-Latency
* Client-Latency

在**RequestStatus = (SAS) NetworkError**的**GetBlob**作業中，如果**最大時間**花在**用戶端延遲**，最常見的問題就是用戶端在儲存體服務中的超時時間過期之前中斷連線。

**建議：**

* 請調查用戶端裡的程式碼，了解用戶端何時及為何與儲存體服務中斷連線。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 調查用戶端的網路連線問題。 

### <a name="put-operation-requeststatus--success"></a>Put 作業： RequestStatus = Success

檢查下列值，如「建議的步驟」一節的步驟5所述：

* 端對端延遲
* Server-Latency
* Client-Latency

在**RequestStatus = Success**的**Put**作業中，如果在**用戶端延遲**時間內花費**最大時間**，這表示用戶端需要更多時間將資料傳送至 Azure 儲存體。 此延遲表示 Client-Side 問題。

**建議：**

* 調查用戶端中的程式碼。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 調查用戶端的網路連線問題。 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put Operation： RequestStatus = (SAS) NetworkError

檢查下列值，如「建議的步驟」一節的步驟5所述：

* 端對端延遲
* Server-Latency
* Client-Latency

在**RequestStatus = (SAS) NetworkError**的**PutBlob**作業中，如果**最大時間**花在**用戶端延遲**，最常見的問題就是用戶端在儲存體服務中的超時時間過期之前中斷連線。

**建議：**

* 請調查用戶端裡的程式碼，了解用戶端何時及為何與儲存體服務中斷連線。
* 使用 Wireshark、Microsoft Message Analyzer 或 Tcping 調查用戶端的網路連線問題。

