---
title: Azure 檔案共用 – 無法刪除 Azure 檔案共用中的檔案
description: 識別和排除從 Azure 檔共用中刪除檔失敗的問題。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196487"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure 檔案共用 – 無法刪除 Azure 檔案共用中的檔案

未能從 Azure 檔共用中刪除檔可能有多種症狀：

**症狀 1：**

由於以下兩個問題之一，未能刪除 Azure 檔共用中的檔：

* 標記為要刪除的檔
* 指定的資源可能由 SMB 用戶端使用

**症狀2：**

Not enough quota is available to process this command

## <a name="cause"></a>原因

當您到達檔允許的併發打開控制碼的上限時，在裝載檔共用的電腦上，將發生錯誤 1816。 有關詳細資訊，請參閱 Azure[存儲性能和可伸縮性檢查表](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)。

## <a name="resolution"></a>解決方案

通過關閉某些控制碼來減少併發打開控制碼的數量。

## <a name="prerequisite"></a>必要條件

### <a name="install-the-latest-azure-powershell-module"></a>安裝最新的 Azure PowerShell 模組

* [安裝 Azure 電源外殼模組](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>連接到 Azure：

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>選擇目標存儲帳戶的訂閱：

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>為目標存儲帳戶創建上下文：

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>獲取檔共用的當前打開控制碼：

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>示例結果：

|控制碼 Id|Path|ClientIp|用戶端埠|開放時間|上次重新連線時間|FileId|父 Id|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12：16：50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12：36：20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12：36：53Z|0|0|9507758546259807489|
|259101229136|新資料夾/測試.zip|10.222.10.123|62758|2019-10-05|12：36：29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|測試.zip|37.222.22.143|62758|2019-10-05|12：36：24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>關閉打開的控制碼：

要關閉打開的控制碼，請使用以下命令：

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>後續步驟

* [針對 Windows 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Linux 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
* [針對 Azure 檔案同步進行移難排解](storage-sync-files-troubleshoot.md)