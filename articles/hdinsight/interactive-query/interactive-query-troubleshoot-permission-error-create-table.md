---
title: Azure HDInsight 中的 Apache Hive 資料表拒絕許可權錯誤
description: 嘗試在 Azure HDInsight 中建立 Apache Hive 資料表時發生許可權拒絕錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 8ebad9300c126193ddb96d5f07057b9a825dbfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "75895150"
---
# <a name="scenario-permission-denied-error-when-trying-to-create-an-apache-hive-table-in-azure-hdinsight"></a>案例：嘗試在 Azure HDInsight 中建立 Apache Hive 資料表時發生許可權拒絕錯誤

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

當您嘗試建立資料表時，將會看到下列錯誤：

```
java.sql.SQLException: Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hdiuser] does not have [ALL] privilege on [wasbs://data@xxxxx.blob.core.windows.net/path/table]
```

如果您執行下列 HDFS 儲存體命令，將會看到類似的錯誤訊息：

```
hdfs dfs -mkdir wasbs://data@xxxxx.blob.core.windows.net/path/table
```

## <a name="cause"></a>原因

在 Apache Hive 中建立資料表的能力，取決於套用至叢集儲存體帳戶的許可權。 如果叢集儲存體帳戶許可權不正確，您將無法建立資料表。 這表示您可以針對資料表建立使用正確的 Ranger 原則，但仍會看到「許可權被拒」錯誤。

## <a name="resolution"></a>解決方案

這是因為使用的儲存體容器上缺乏足夠的許可權。 建立 Hive 資料表的使用者需要容器的讀取、寫入和執行許可權。 如需詳細資訊，請參閱 [在 HDP 2.2 中使用 Apache Ranger 進行 Hive 授權的最佳做法](https://hortonworks.com/blog/best-practices-for-hive-authorization-using-apache-ranger-in-hdp-2-2/)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
