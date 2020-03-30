---
title: 阿帕奇·希澤佩林解譯器錯誤 - Azure HDInsight
description: Apache Zeppelin Hive JDBC 解譯器指向 Azure HDInsight 中的錯誤 URL
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895067"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>場景：Apache Hive Zepelin 解譯器在 Azure HDInsight 中給出動物園管理員錯誤

本文介紹了在 Azure HDInsight 群集中使用互動式查詢元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

在 Apache Hive LLAP 群集上，Zepelin 解譯器在嘗試執行查詢時給出以下錯誤訊息：

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>原因

澤佩林HiveJDBC口譯員指向了錯誤的 URL。

## <a name="resolution"></a>解決方案

1. 導航到 Hive 元件摘要並將"Hive JDBC Url"複製到剪貼簿。

1. 巡覽到 `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. 編輯 JDBC 設置：將 hive.url 值更新為步驟 1 中複製的 Hive JDBC URL

1. 保存，然後重試查詢

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
