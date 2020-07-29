---
title: Azure HDInsight 中的連接重設後發生儲存例外狀況
description: Azure HDInsight 中的連接重設後發生儲存例外狀況
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887218"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>案例： Azure HDInsight 中的連接重設之後發生儲存例外狀況

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

無法建立新的 Apache HBase 資料表。

## <a name="cause"></a>原因

在資料表截斷過程中，發生儲存體連接問題。 已在 HBase 中繼資料表中刪除資料表專案。 但只刪除一個 blob 檔案。

雖然存放裝置中沒有名為的資料夾 blob `/hbase/data/default/ThatTable` 。 WASB 驅動程式發現 blob 檔案上方存在，因此不允許建立任何名為的 blob `/hbase/data/default/ThatTable` ，因為它假設父資料夾已存在，因此建立資料表將會失敗。

## <a name="resolution"></a>解決方案

1. 從 Apache Ambari UI，重新開機使用中的 HMaster。 這可讓兩個待命 HMaster 的其中一個變成作用中的，而新的作用中 HMaster 將會重載中繼資料資料表資訊。 因此，您不會 `already-deleted` 在 HMASTER UI 中看到該資料表。

1. 您可以從 UI 工具（例如 Cloud Explorer 或執行類似的命令）找到孤立的 blob 檔案 `hdfs dfs -ls /xxxxxx/yyyyy` 。 執行 `hdfs dfs -rmr /xxxxx/yyyy` 以刪除該 blob。 例如： `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile` 。

現在您可以在 HBase 中建立具有相同名稱的新資料表。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
