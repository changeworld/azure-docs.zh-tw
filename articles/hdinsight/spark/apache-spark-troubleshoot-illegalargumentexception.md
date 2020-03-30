---
title: Apache Spark 的非法參數異常錯誤 - Azure HDInsight
description: Azure 資料工廠 Azure HDInsight 中 Apache Spark 活動的非法參數例外
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894387"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>方案：Azure HDInsight 中 Apache Spark 活動的非法參數例外

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

嘗試在 Azure 資料工廠管道中執行 Spark 活動時，您會收到以下異常：

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

如果應用程式 jar 檔未位於 Spark 群集的預設/主存儲中，Spark 作業將失敗。

這是一個已知的問題與在這個Bug中跟蹤的Spark開源框架：[如果fs.defaultFS和應用程式jar是不同的URL，Spark作業將失敗](https://issues.apache.org/jira/browse/SPARK-22587)。

此問題已在 Spark 2.3.0 中解決。

## <a name="resolution"></a>解決方案

確保應用程式 jar 存儲在 HDInsight 群集的預設/主存儲上。 對於 Azure 資料工廠，請確保 ADF 連結服務指向 HDInsight 預設容器，而不是輔助容器。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
