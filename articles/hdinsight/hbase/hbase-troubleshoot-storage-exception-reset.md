---
title: Azure HDInsight 中連接重置後的存儲異常
description: Azure HDInsight 中連接重置後的存儲異常
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887218"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中重置連接後存儲異常

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

無法創建新的 Apache HBase 表。

## <a name="cause"></a>原因

在表截斷過程中，存在存儲連接問題。 表條目在 HBase 中繼資料表中被刪除。 除一個 Blob 檔，所有檔都已被刪除。

儘管存儲中沒有稱為"資料夾`/hbase/data/default/ThatTable`blob"的資料夾 Blob。 WASB 驅動程式發現存在上述 blob 檔，並且不允許創建任何調用`/hbase/data/default/ThatTable`的 blob，因為它假定存在父資料夾，因此創建表將失敗。

## <a name="resolution"></a>解決方案

1. 從 Apache Ambari UI，重新開機活動的 HMaster。 這將允許兩個備用 HMaster 之一成為活動 HMaster 之一，而新的活動 HMaster 將重新載入中繼資料表資訊。 因此，`already-deleted`您將不會在 HMaster UI 中看到該表。

1. 可以從 UI 工具（如雲資源管理器）或運行命令（如`hdfs dfs -ls /xxxxxx/yyyyy`）中找到孤立 blob 檔。 運行`hdfs dfs -rmr /xxxxx/yyyy`以刪除該 Blob。 例如： `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile` 。

現在，您可以在 HBase 中創建同名的新表。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
