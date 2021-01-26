---
title: 使用 REST 介面 Azure 資料箱疑難排解 |Microsoft Docs
description: 說明如何針對透過 REST 介面複製資料時，在 Azure 資料箱中看到的問題進行疑難排解。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 17b8d6de198746a79a50c4fbda805b364212e3c4
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796060"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>針對 Azure 資料箱 Blob 儲存體的相關問題進行疑難排解

本文將詳細說明如何針對您透過資料箱上的 REST 介面使用資料箱 Blob 儲存體來複製資料時，可能會看到的問題進行疑難排解。 當您使用資料箱 Blob 儲存體搭配其他應用程式或用戶端程式庫（例如適用于 Python 的 Azure 儲存體總管、AzCopy 或 Azure 儲存體程式庫）時，就會發生這些問題。

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure 儲存體總管中看到的錯誤

本節將詳細說明搭配使用 Azure 儲存體總管與資料箱 Blob 儲存體時所面臨的一些問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|無法取出子資源。 其中一個 HTTP 標頭之值的格式不正確。|從 [ **編輯** ] 功能表中選取 [ **目標 Azure Stack api**]。 <br>重新開機 Azure 儲存體總管。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至位於此路徑的 hosts 檔案： <li>`C:\Windows\System32\drivers\etc\hosts` 在 Windows 上，或 </li><li> `/etc/hosts` 在 Linux 上。</li>|
|無法取出子資源。 <br>詳細資料：自我簽署憑證 |將裝置的 TLS/SSL 憑證匯入 Azure 儲存體總管： <li>從 Azure 入口網站下載憑證。 如需詳細資訊，請移至 [下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)。</li><li>從 [ **編輯** ] 功能表選取 [ **SSL 憑證** ]，然後選取 [匯 **入憑證**]。</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>AzCopy for Windows 中看到的錯誤

本節將詳細說明使用 AzCopy for Windows 搭配資料箱 Blob 儲存體時所面臨的一些問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|顯示此錯誤之前，AzCopy 命令似乎停止回應一分鐘： <br>無法列舉目錄 HTTPs://.。。 無法解析遠端名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至 hosts 檔案，網址為： `C:\Windows\System32\drivers\etc\hosts` 。|
|顯示此錯誤之前，AzCopy 命令似乎停止回應一分鐘： <br>剖析來源位置時發生錯誤。 基礎連接已關閉：無法為 SSL/TLS 安全通道建立信任關係。|將裝置的 TLS/SSL 憑證匯入系統的憑證存放區中。 如需詳細資訊，請移至 [下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)。|


## <a name="errors-seen-in-azcopy-for-linux"></a>AzCopy for Linux 中出現的錯誤

本節將詳細說明使用 AzCopy for Linux 搭配資料箱 Blob 儲存體時所面臨的一些問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|AzCopy 命令似乎停止回應20分鐘，再顯示此錯誤： <br>剖析來源位置時發生錯誤 `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>` 。 無此裝置或位址|檢查端點名稱 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` 是否已新增至 hosts 檔案，網址為： `/etc/hosts` 。|
|AzCopy 命令似乎停止回應20分鐘，再顯示此錯誤： <br>剖析來源位置時發生錯誤 .。。 無法建立 SSL 連線。|將裝置的 TLS/SSL 憑證匯入系統的憑證存放區中。 如需詳細資訊，請移至 [下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)。|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Python Azure 儲存體程式庫中看到的錯誤

本節詳細說明一些當使用 Linux 用戶端進行資料複製時，於資料箱磁碟部署期間最常面臨的問題。

|錯誤訊息  |建議的動作 |
|---------|---------|
|其中一個 HTTP 標頭之值的格式不正確。 |資料箱不支援已安裝的 Python Microsoft Azure 儲存體程式庫版本。 如需支援的版本，請參閱 Azure 資料箱 Blob 儲存體需求。|
|… [SSL： CERTIFICATE_VERIFY_FAILED] .。。|執行 Python 之前，請將 REQUESTS_CA_BUNDLE 環境變數設定為 Base64 編碼的 TLS 憑證檔案的路徑， (瞭解如何 [下載憑證](data-box-deploy-copy-data-via-rest.md#download-certificate)) 。 <br>例如：<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>或者，將憑證新增至系統的憑證存放區，然後將此環境變數設定為該存放區的路徑。 <br> 例如，在 Ubuntu 上︰  <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>常見錯誤

這些錯誤不是任何應用程式特有的。

|錯誤訊息  |建議的動作 |
|---------|---------|
|連接逾時。 |登入資料箱裝置，並檢查它是否已解除鎖定。 裝置重新開機時，會一直鎖定，直到有人登入為止。|
|REST API 驗證失敗，並出現錯誤：伺服器無法驗證要求。 請確定授權標頭值的格式正確，包括簽章。 ErrorCode： AuthenticationFailed。 |發生這種情況的其中一個原因是裝置時間未與 Azure 同步。 如果有大量的時間扭曲，當您嘗試透過 REST API 將資料複製到資料箱時，REST API 的驗證就會中斷。 在這種情況下，您可以開啟輸出 UDP 123 埠，以允許存取 `time.windows.com` 。 一旦裝置時間與 Azure 同步，驗證應該會成功。 |

## <a name="next-steps"></a>後續步驟

- 瞭解 [資料箱 Blob 儲存體系統需求](data-box-system-requirements-rest.md)。
