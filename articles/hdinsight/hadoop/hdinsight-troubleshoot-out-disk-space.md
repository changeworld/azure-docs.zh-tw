---
title: 叢集節點的磁碟空間已用盡 Azure HDInsight
description: 針對 Azure HDInsight 中的 Apache Hadoop 叢集節點磁碟空間問題進行疑難排解。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: f1a994ad07980c67e37d00bffb7e605ed610bb08
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289083"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>案例：叢集節點的磁碟空間已用盡 Azure HDInsight

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

作業可能會失敗，並出現類似以下的錯誤訊息： `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

或者，您可能會收到類似下列的 Apache Ambari 警示： `local-dirs usable space is below configured utilization percentage` 。

## <a name="cause"></a>原因

Apache Yarn 應用程式快取可能已耗用所有可用磁碟空間。 您的 Spark 應用程式可能會以效率不高的狀態執行。

## <a name="resolution"></a>解決方法

1. 使用 Ambari UI 來判斷哪個節點的磁碟空間已用盡。

1. 判斷麻煩節點中的哪個資料夾對大部分的磁碟空間有貢獻。 先透過 SSH 連線到節點，然後執行 `df` 以列出所有掛接的磁片使用量。 通常這是 `/mnt` OSS 所使用的暫存磁片。 您可以在資料夾中輸入，然後輸入 `sudo du -hs` 以顯示資料夾下的摘要檔案大小。 如果您看到類似的資料夾 `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` ，表示應用程式仍在執行中。 這可能是因為 RDD 持續性或中繼隨機檔案所致。

1. 若要減輕此問題，請終止應用程式，這將會釋放該應用程式所使用的磁碟空間。

1. 如果背景工作節點上的問題經常發生，您可以在叢集上調整 YARN 本機快取設定。

    開啟 Ambari UI 流覽至 [YARN]--> [> Advanced]。  
    將下列兩個屬性新增至自訂 yarn-site.xml 區段，並儲存：

    ```
    yarn.nodemanager.localizer.cache.target-size-mb=2048
    yarn.nodemanager.localizer.cache.cleanup.interval-ms=300000
    ```

1. 如果上述程式未永久修正問題，請將您的應用程式優化。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]