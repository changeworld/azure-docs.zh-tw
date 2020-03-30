---
title: 正在訪問的帳戶不支援 Azure HDInsight 中的 HTTP 錯誤
description: 本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165549"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>正在訪問的帳戶不支援 Azure HDInsight 中的 HTTP 錯誤

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

收到以下錯誤訊息：

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>原因

收到錯誤訊息的原因有多種：

* 存儲帳戶已啟用[安全轉移](../../storage/common/storage-require-secure-transfer.md)，並且正在使用不正確的[URI 方案](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。

* 使用*禁用*安全轉移的存儲帳戶創建群集。 此後，存儲帳戶上啟用了安全轉移。

## <a name="resolution"></a>解決方案

如果為 Azure 存儲或資料湖存儲 Gen2 啟用了安全傳輸，`wasbs://`則`abfss://`URI 將分別為 或 。  另請參閱[安全傳輸](../../storage/common/storage-require-secure-transfer.md)。

對於新群集，請使用已具有所需安全傳輸設置的存儲帳戶。 不要更改現有群集正在使用的存儲帳戶的安全轉移設置。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
