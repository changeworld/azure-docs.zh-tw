---
title: 使用 REST 介面的 Azure 資料框故障排除*微軟文檔
description: 描述如何在資料拷貝通過 REST 介面時解決 Azure 資料框中看到的問題。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297134"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>解決與 Azure 資料盒 Blob 存儲相關的問題

本文詳細介紹了如何在使用資料盒上的 REST 介面使用資料盒 Blob 存儲來複製資料時看到的問題。 當您將資料盒 Blob 存儲與其他應用程式或用戶端庫（如 Azure 存儲資源管理器、AzCopy 或 Python Azure 存儲庫）一起使用時，這些問題會浮出水面。

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure 存儲資源管理器中看到的錯誤

本節詳細介紹了使用 Azure 存儲資源管理器與資料盒 Blob 存儲時所面臨的一些問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|無法檢索子資源。 其中一個 HTTP 標頭之值的格式不正確。|在 **"編輯"** 功能表中，選擇**目標 Azure 堆疊 API**。 <br>重新開機 Azure 存儲資源管理器。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |檢查終結點名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`是否添加到此路徑的主機檔中： <li>`C:\Windows\System32\drivers\etc\hosts`在 Windows 上，或 </li><li> `/etc/hosts`在Linux上。</li>|
|無法檢索子資源。 <br>詳細資訊：自簽章憑證 |將設備的 TLS/SSL 憑證導入 Azure 存儲資源管理器： <li>從 Azure 門戶下載證書。 有關詳細資訊，請轉到[下載證書](data-box-deploy-copy-data-via-rest.md#download-certificate)。</li><li>在 **"編輯"** 功能表中，選擇**SSL 憑證**，然後選擇 **"導入證書**"。</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>在 Windows 的 AzCopy 中看到的錯誤

本節詳細介紹了使用具有資料框 Blob 存儲的 Windows 的 AzCopy 時所面臨的一些問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|在顯示此錯誤之前，AzCopy 命令似乎掛起了一分鐘： <br>無法枚舉目錄HTTPs://... 無法解析遠端名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|檢查終結點名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`是否添加到主機檔中： `C:\Windows\System32\drivers\etc\hosts`|
|在顯示此錯誤之前，AzCopy 命令似乎掛起了一分鐘： <br>分析源位置的錯誤。 基礎連接已關閉：無法為 SSL/TLS 安全通道建立信任關係。|將設備的 TLS/SSL 憑證導入系統的憑證存放區。 有關詳細資訊，請轉到[下載證書](data-box-deploy-copy-data-via-rest.md#download-certificate)。|


## <a name="errors-seen-in-azcopy-for-linux"></a>在 Linux 的 AzCopy 中看到的錯誤

本節詳細介紹了使用適用于 Linux 的 AzCopy 與資料盒 Blob 存儲一起時所面臨的一些問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|在顯示此錯誤之前，AzCopy 命令似乎掛起了 20 分鐘： <br>錯誤分析源位置`https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`。 無此裝置或位址|檢查終結點名稱`<accountname>.blob.<serialnumber>.microsoftdatabox.com`是否添加到主機檔中： `/etc/hosts`|
|在顯示此錯誤之前，AzCopy 命令似乎掛起了 20 分鐘： <br>分析源位置的錯誤... 無法建立 SSL 連線。|將設備的 TLS/SSL 憑證導入系統的憑證存放區。 有關詳細資訊，請轉到[下載證書](data-box-deploy-copy-data-via-rest.md#download-certificate)。|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>在 Python 的 Azure 存儲庫中看到的錯誤

本節詳細說明一些當使用 Linux 用戶端進行資料複製時，於資料箱磁碟部署期間最常面臨的問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|其中一個 HTTP 標頭之值的格式不正確。 |資料框不支援為 Python 安裝的 Microsoft Azure 存儲庫的版本。 有關受支援的版本，請參閱 Azure 資料盒 Blob 存儲要求。|
|… [SSL：CERTIFICATE_VERIFY_FAILED] ...|在運行 Python 之前，將REQUESTS_CA_BUNDLE環境變數設置為 Base64 編碼的 TLS 證書檔的路徑（請參閱如何[下載證書](data-box-deploy-copy-data-via-rest.md#download-certificate)）。 <br>例如：<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>或者，將證書添加到系統的憑證存放區，然後將此環境變數設置為該存儲的路徑。 <br> 例如，在 Ubuntu 上︰  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>常見錯誤

這些錯誤不特定于任何應用程式。

|錯誤訊息  |建議的動作 |
|---------|---------|
|連接逾時。 |登錄到資料框設備並檢查其是否已解鎖。 每當設備重新開機時，設備將保持鎖定狀態，直到有人登錄。|

## <a name="next-steps"></a>後續步驟

- 瞭解[資料盒 Blob 存儲系統要求](data-box-system-requirements-rest.md)。
