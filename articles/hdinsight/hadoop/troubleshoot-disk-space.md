---
title: 管理 Azure HDInsight 中的磁碟空間
description: 針對與 Azure HDInsight 叢集互動時的問題進行疑難排解的步驟和可能的解決方式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77473008"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>管理 Azure HDInsight 中的磁碟空間

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="hive-log-configurations"></a>Hive 記錄檔設定

1. 從網頁瀏覽器流覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

1. 流覽至**hive** > 配置 > **advanced** > **advanced Hive-log4j**。 請檢查下列設定：

    * `hive.root.logger=DEBUG,RFA`第 1 課：建立 Windows Azure 儲存體物件{2}。 這是預設值，將[記錄層級](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html)修改為 `INFO` 以列印較少的記錄專案。

    * `log4jhive.log.maxfilesize=1024MB`第 1 課：建立 Windows Azure 儲存體物件{2}。 這是預設值，請視需要進行修改。

    * `log4jhive.log.maxbackupindex=10`第 1 課：建立 Windows Azure 儲存體物件{2}。 這是預設值，請視需要進行修改。 如果省略了參數，產生的記錄檔將會無限。

## <a name="yarn-log-configurations"></a>Yarn 記錄檔設定

請檢查下列設定：

* Apache Ambari

    1. 從網頁瀏覽器流覽至 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是叢集的名稱。

    1. 流覽至**Hive** > 配置 > **Advanced** > **Resource Manager**。 確定已核取 [**啟用記錄匯總**]。 如果停用，名稱節點會將記錄檔保留在本機，而不會在應用程式完成或終止時，將它們匯總在遠端存放區。

* 請確定叢集大小適用於工作負載。 工作負載可能最近已變更，或叢集可能已調整大小。 相應[增加](../hdinsight-scaling-best-practices.md)叢集，以符合較高的工作負載。

* `/mnt/resource` 可能會填入孤立的檔案（如 resource manager 重新開機的情況）。 如有必要，請手動清除 `/mnt/resource/hadoop/yarn/log` 並 `/mnt/resource/hadoop/yarn/local`。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 與[@AzureSupport](https://twitter.com/azuresupport)進行連接-官方 Microsoft Azure 帳戶，以改善客戶體驗。 將 Azure 社區連接到正確的資源：解答、支援和專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
