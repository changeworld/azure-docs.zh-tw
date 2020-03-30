---
title: 阿帕奇蜂巢中的聯接會導致記憶體不足錯誤 - Azure HDInsight
description: 處理記憶體不足錯誤"GC 開銷限制超過錯誤"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895179"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>方案：在 Apache Hive 中加入會導致 Azure HDInsight 中記憶體不足錯誤

本文介紹了在 Azure HDInsight 群集中使用互動式查詢元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

Apache Hive 聯接的預設行為是將表的整個內容載入到記憶體中，以便無需執行 Map/Reduce 步驟即可執行聯接。 如果 Hive 表太大，無法放入記憶體中，則查詢可能會失敗。

## <a name="cause"></a>原因

在足夠大大小的配置單元中運行聯接時，會遇到以下錯誤：

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解決方案

通過設置以下 Hive 配置值，防止 Hive 在聯接（改為執行映射/減少步驟）時將表載入到記憶體中：

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>後續步驟

如果設置此值無法解決您的問題，請訪問以下...

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
