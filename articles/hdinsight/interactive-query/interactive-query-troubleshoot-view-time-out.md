---
title: Apache Hive 從查詢結果中查看時間-Azure HDInsight
description: Apache Hive 在 Azure HDInsight 中提取查詢結果時看到時間
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f00f70e674ac0b83b737d6b2a4bf9d20400736fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77672016"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>案例：在 Azure HDInsight 中提取查詢結果時，Apache Hive 查看超時

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

從 Apache Hive 視圖執行特定查詢時，可能會遇到下列錯誤：

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>原因

Hive View default timeout 值可能不適用於您正在執行的查詢。 指定的時間週期太短，Hive 視圖無法提取查詢結果。

## <a name="resolution"></a>解決方案

在中設定下列屬性，以增加 Apache Ambari Hive 視圖的超時 `/etc/ambari-server/conf/ambari.properties` 。

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

的值 `HIVE_VIEW_INSTANCE_NAME` 可在 Hive VIEW URL 結尾處取得。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
