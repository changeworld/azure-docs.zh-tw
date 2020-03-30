---
title: 當 Azure HDInsight 存儲具有許多檔時，Apache Spark 速度變慢
description: 當 Azure 存儲容器包含 Azure HDInsight 中的許多檔時，Apache Spark 作業運行緩慢
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894333"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>當 Azure 儲存體容器在 Azure HDInsight 中包含許多檔案時，Apache Spark 的作業執行速度會變慢

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

運行 HDInsight 群集時，當有許多檔/子資料夾時，寫入 Azure 存儲容器的 Apache Spark 作業會變慢。 例如，寫入新容器時需要 20 秒，但寫入具有 200k 檔的容器時大約需要 2 分鐘。

## <a name="cause"></a>原因

這是一個已知的 Spark 問題。 在 Spark 作業執行`ListBlob`期間`GetBlobProperties`，慢速來自 和 操作。

要跟蹤分區，Spark 必須維護包含目錄`FileStatusCache`結構資訊的 。 使用此緩存，Spark 可以解析路徑並瞭解可用的分區。 跟蹤分區的好處是 Spark 僅在讀取資料時才接觸必要的檔。 為了保持此資訊最新，當您寫入新資料時，Spark 必須列出目錄下的所有檔並更新此緩存。

在 Spark 2.1 中，雖然我們不需要在每次寫入後更新緩存，但 Spark 將檢查現有分區列是否與當前寫入請求中建議的分區列匹配，因此它也會導致在每次寫入開始時列出操作。

在 Spark 2.2 中，使用追加模式寫入資料時，應修復此性能問題。

## <a name="resolution"></a>解決方案

創建分區資料集時，請務必使用分區方案，以限制 Spark 必須列出的檔數來更新`FileStatusCache`。

對於 N % 100 = 0（100 只是示例）的每個 N 第 N 個微批次處理，將現有資料移動到另一個目錄，Spark 可以載入該目錄。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
