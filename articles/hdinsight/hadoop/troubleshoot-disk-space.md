---
title: 管理 Azure HDInsight 中的磁碟空間
description: 在與 Azure HDInsight 群集交互時，對問題的步驟和可能的解決方案進行故障排除。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473008"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>管理 Azure HDInsight 中的磁碟空間

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="hive-log-configurations"></a>蜂巢日誌配置

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名稱在哪裡。

1. 導航到**蜂巢** > **Configs** > **Advanced**高級 > **蜂巢-log4j**。 查看以下設置：

    * `hive.root.logger=DEBUG,RFA`. 這是預設值，修改[日誌級別](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html)以`INFO`列印較少的日誌條目。

    * `log4jhive.log.maxfilesize=1024MB`. 這是預設值，根據需要進行修改。

    * `log4jhive.log.maxbackupindex=10`. 這是預設值，根據需要進行修改。 如果省略了參數，則生成的日誌檔將是無窮無盡的。

## <a name="yarn-log-configurations"></a>紗線日誌配置

查看以下配置：

* Apache Ambari

    1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名稱在哪裡。

    1. 導航到**Hive** > **Configs** > **高級** > **資源管理器**。 確保選中**啟用日誌聚合**。 如果禁用，名稱節點將保留日誌本地，不會在應用程式完成或終止時將它們聚合到遠端存放中。

* 請確定叢集大小適用於工作負載。 工作負載最近可能已更改，或者群集可能已調整大小。 [向上擴展](../hdinsight-scaling-best-practices.md)群集以匹配更高的工作負載。

* `/mnt/resource`可能填充孤立檔案（如資源管理器重新開機的情況）。 如有必要，請手動清潔`/mnt/resource/hadoop/yarn/log`和`/mnt/resource/hadoop/yarn/local`。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
