---
title: Azure HDInsight 中 Apache Hive 資料表的許可權被拒錯誤
description: 嘗試在 Azure HDInsight 中建立 Apache Hive 資料表時發生許可權拒絕錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895150"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>案例：嘗試在 Azure HDInsight 中建立 Apache Hive 資料表時，許可權被拒絕錯誤

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

嘗試建立資料表時，您會看到下列錯誤：

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

如果您執行下列 HDFS 儲存體命令，將會看到類似的錯誤訊息：

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>原因

在 Apache Hive 中建立資料表的功能是由套用至叢集儲存體帳戶的許可權決定。 如果叢集儲存體帳戶的許可權不正確，您將無法建立資料表。 這表示您可以有正確的 Ranger 原則來建立資料表，而且仍然會看到「許可權被拒」錯誤。

## <a name="resolution"></a>解決方法

這是因為使用的儲存體容器上缺乏足夠的許可權。 建立 Hive 資料表的使用者需要容器的讀取、寫入和執行許可權。 如需詳細資訊，請參閱[在 HDP 2.2 中使用 Apache Ranger 進行 Hive 授權的最佳做法](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
