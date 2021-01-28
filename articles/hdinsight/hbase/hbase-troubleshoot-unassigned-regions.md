---
title: Azure HDInsight 中的區域伺服器問題
description: Azure HDInsight 中的區域伺服器問題
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: 968a0c6e1717245171bf84821a58cad4e440046e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936606"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight 中的區域伺服器問題

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="scenario-unassigned-regions"></a>案例：未指派的區域

### <a name="issue"></a>問題

`hbase hbck`執行命令時，您會看到類似下列的錯誤訊息：

```
multiple regions being unassigned or holes in the chain of regions
```

從 Apache HBase Master UI，您可以查看所有區域伺服器之間的不平衡區域數目。 然後，您可以執行 `hbase hbck` 命令來查看區域鏈結中的漏洞。

### <a name="cause"></a>原因

漏洞可能是離線區域的結果。

### <a name="resolution"></a>解決方案

修正指派。 請遵循下列步驟，讓未指派的區域恢復正常狀態：

1. 使用 SSH 登入 HDInsight HBase 叢集。

1. 執行 `hbase zkcli` 命令以使用 ZooKeeper shell 進行連接。

1. 執行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。

1. 使用命令結束 zookeeper shell `exit` 。

1. 開啟 Apache Ambari UI，然後重新啟動 Active HBase Master 服務。

1. `hbase hbck`重新執行命令 (沒有任何進一步的選項) 。 檢查輸出，並確定已指派所有區域。

---

## <a name="scenario-dead-region-servers"></a>案例：死的區域伺服器

### <a name="issue"></a>問題

區域伺服器無法啟動。

### <a name="cause"></a>原因

多個分割 WAL 目錄。

1. 取得目前 Wal 的清單： `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` 。

1. 檢查檔案 `wals.out` 。 如果有太多的分割目錄 (從 * 分割) 開始，區域伺服器可能會因為這些目錄而失敗。

### <a name="resolution"></a>解決方案

1. 從 Ambari 入口網站停止 HBase。

1. 執行 `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` 以取得 wal 的新清單。

1. 將 * 分割目錄移至暫存資料夾， `splitWAL` 然後刪除 * 分割目錄。

1. 執行 `hbase zkcli` 命令以使用 zookeeper shell 進行連接。

1. 執行 `rmr /hbase-unsecure/splitWAL`。

1. 重新開機 HBase 服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。