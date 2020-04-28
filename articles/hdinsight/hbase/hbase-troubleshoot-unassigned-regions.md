---
title: Azure HDInsight 中區域伺服器的問題
description: Azure HDInsight 中區域伺服器的問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79272756"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight 中區域伺服器的問題

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="scenario-unassigned-regions"></a>案例：未指派的區域

### <a name="issue"></a>問題

執行`hbase hbck`命令時，您會看到類似下列的錯誤訊息：

```
multiple regions being unassigned or holes in the chain of regions
```

從 Apache HBase Master UI 中，您可以看到所有區域伺服器之間不平衡的區域數目。 然後，您可以執行 `hbase hbck` 命令來查看區域鏈結中的漏洞。

### <a name="cause"></a>原因

漏洞可能是離線區域的結果。

### <a name="resolution"></a>解決方法

修正指派。 請遵循下列步驟，讓未指派的區域恢復正常狀態：

1. 使用 SSH 登入 HDInsight HBase 叢集。

1. 執行`hbase zkcli`命令以使用 ZooKeeper shell 進行連接。

1. 執行`rmr /hbase/regions-in-transition`或`rmr /hbase-unsecure/regions-in-transition`命令。

1. 使用`exit`命令結束 zookeeper shell。

1. 開啟 Apache Ambari UI，然後重新啟動 Active HBase Master 服務。

1. 再次`hbase hbck`執行命令（不含任何進一步的選項）。 檢查輸出，並確定已指派所有區域。

---

## <a name="scenario-dead-region-servers"></a>案例：無作用的區域伺服器

### <a name="issue"></a>問題

區域伺服器無法啟動。

### <a name="cause"></a>原因

多個分割 WAL 目錄。

1. 取得目前 Wal 的清單： `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`。

1. `wals.out`檢查檔案。 如果有太多分割目錄（從 *-分割開始），區域伺服器可能會因為這些目錄而失敗。

### <a name="resolution"></a>解決方法

1. 從 Ambari 入口網站停止 HBase。

1. 執行`hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`以取得最新的 wal 清單。

1. 將 *-分割目錄移至暫存資料夾， `splitWAL`並刪除 * 分割目錄。

1. 執行`hbase zkcli`命令以使用 zookeeper shell 進行連接。

1. 執行 `rmr /hbase-unsecure/splitWAL`。

1. 重新開機 HBase 服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
