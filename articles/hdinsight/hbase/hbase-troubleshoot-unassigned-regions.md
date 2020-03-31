---
title: Azure HDInsight 中的區域伺服器問題
description: Azure HDInsight 中的區域伺服器問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272756"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Azure HDInsight 中的區域伺服器問題

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="scenario-unassigned-regions"></a>方案：未分配區域

### <a name="issue"></a>問題

運行`hbase hbck`命令時，您會看到類似于以下錯誤的錯誤訊息：

```
multiple regions being unassigned or holes in the chain of regions
```

從 Apache HBase 主 UI 中，您可以看到所有區域伺服器不平衡的區域數。 然後，您可以執行 `hbase hbck` 命令來查看區域鏈結中的漏洞。

### <a name="cause"></a>原因

孔可能是離線區域的結果。

### <a name="resolution"></a>解決方案

修復分配。 請遵循下列步驟，讓未指派的區域恢復正常狀態：

1. 使用 SSH 登錄到 HDInsight HBase 群集。

1. 運行`hbase zkcli`命令以與 ZooKeeper 外殼連接。

1. 運行`rmr /hbase/regions-in-transition`或`rmr /hbase-unsecure/regions-in-transition`命令。

1. 使用`exit`命令退出動物園管理員外殼。

1. 開啟 Apache Ambari UI，然後重新啟動 Active HBase Master 服務。

1. 再次`hbase hbck`運行命令（沒有任何進一步的選項）。 檢查輸出並確保分配所有區域。

---

## <a name="scenario-dead-region-servers"></a>方案：死區伺服器

### <a name="issue"></a>問題

區域伺服器無法啟動。

### <a name="cause"></a>原因

多個拆分 WAL 目錄。

1. 獲取當前 WAL 的清單`hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`： 。

1. 檢查`wals.out`檔。 如果拆分目錄太多（從 *拆分開始），則區域伺服器可能由於這些目錄而失敗。

### <a name="resolution"></a>解決方案

1. 從安巴里門戶停止 HBase。

1. 執行`hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`以獲取新的 WAL 清單。

1. 將 _-拆分目錄移動到暫存檔案夾，`splitWAL`並刪除 _-拆分目錄。

1. 執行`hbase zkcli`命令以與動物園管理員外殼連接。

1. 執行 `rmr /hbase-unsecure/splitWAL`。

1. 重新開機 HBase 服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
