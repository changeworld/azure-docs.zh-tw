---
title: Apache Hive Zeppelin 解譯器錯誤-Azure HDInsight
description: Apache Zeppelin Hive JDBC 解譯器在中指向錯誤的 URL Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 20309babb9ece0ae20e7442543b0d378f9a51060
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895067"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>案例： Apache Hive Zeppelin 解譯器在 Azure HDInsight 中提供了 Zookeeper 錯誤

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

在 Apache Hive LLAP 叢集上，Zeppelin 解譯器會在嘗試執行查詢時提供下列錯誤訊息：

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>原因

Zeppelin Hive JDBC 解譯器指向錯誤的 URL。

## <a name="resolution"></a>解決方法

1. 流覽至 [Hive 元件摘要]，並將 [Hive JDBC Url] 複製到剪貼簿。

1. 巡覽到 `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. 編輯 JDBC 設定：將 hive. url 值更新為在步驟1中複製的 Hive JDBC URL

1. 儲存，然後再次嘗試查詢

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
