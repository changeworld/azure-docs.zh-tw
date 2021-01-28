---
title: Azure HDInsight 儲存體有許多檔案時 Apache Spark 緩慢
description: 當 Azure 儲存體容器包含許多檔案時，Apache Spark 作業會緩慢執行 Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: c26baec66248ca00ef212acf3d773c2566b3aea9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946354"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>當 Azure 儲存體容器在 Azure HDInsight 中包含許多檔案時，Apache Spark 的作業執行速度會變慢

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

執行 HDInsight 叢集時，當有許多檔案/子資料夾時，寫入至 Azure 儲存體容器的 Apache Spark 作業會變慢。 例如，寫入至新的容器時需要20秒的時間，但在寫入具有200k 檔案的容器時大約需要2分鐘。

## <a name="cause"></a>原因

這是已知的 Spark 問題。 緩慢來自于 `ListBlob` `GetBlobProperties` Spark 作業執行期間的和作業。

若要追蹤磁碟分割，Spark 必須維護， `FileStatusCache` 其中包含目錄結構的相關資訊。 使用此快取，Spark 可以剖析路徑並留意可用的磁碟分割。 追蹤資料分割的優點在於，只有當您讀取資料時，Spark 才會觸及必要的檔案。 為了讓這份資訊保持在最新狀態，當您撰寫新的資料時，Spark 必須列出目錄下的所有檔案，並更新此快取。

在 Spark 2.1 中，雖然不需要在每次寫入之後更新快取，但 Spark 會檢查現有的資料分割資料行是否與目前寫入要求中的建議專案相符，因此也會導致每次寫入開始時列出作業。

在 Spark 2.2 中，使用附加模式來寫入資料時，應該修正此效能問題。

## <a name="resolution"></a>解決方案

當您建立資料分割的資料集時，請務必使用資料分割配置，以限制 Spark 必須列出以更新的檔案數目 `FileStatusCache` 。

針對 N %100 = = 0 (100 只是範例) 的每 n 個微批次，請將現有的資料移至另一個可由 Spark 載入的目錄。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]