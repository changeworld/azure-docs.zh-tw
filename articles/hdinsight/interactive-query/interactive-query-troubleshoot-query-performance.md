---
title: Azure HDInsight 中的 Apache Hive LLAP 查詢性能不佳
description: Apache Hive LLAP 中的查詢在 Azure HDInsight 中執行得比預期慢。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8bd20849b15f8c8d5a14653f702f78c6404d82e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895132"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Hive LLAP 查詢性能不佳

本文介紹了在 Azure HDInsight 群集中使用互動式查詢元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

預設群集配置未針對工作負荷進行充分調整。 Hive LLAP 中的查詢執行速度比預期的慢。

## <a name="cause"></a>原因

由於各種原因，這種情況可能發生。

## <a name="resolution"></a>解決方案

LLAP 針對涉及聯接和聚合的查詢進行了優化。 以下查詢在互動式 Hive 群集中表現不佳：

```
select * from table where column = "columnvalue"
```

為了提高 Hive LLAP 中的點查詢性能，請設置以下配置：

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

您還可以通過以下配置更改提高 LLAP 緩存的使用率，以提高性能：

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
