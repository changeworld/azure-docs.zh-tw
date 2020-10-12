---
title: 叢集節點的磁碟空間已用盡 Azure HDInsight
description: 針對 Azure HDInsight 中的 Apache Hadoop 叢集節點磁碟空間問題進行疑難排解。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.openlocfilehash: ead79ca0a37a270f03a305064c80426553db59ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82628532"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>案例：叢集節點的磁碟空間已用盡 Azure HDInsight

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

作業可能會失敗，並出現類似以下的錯誤訊息： `/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

或者，您可能會收到類似下列的 Apache Ambari 警示： `local-dirs usable space is below configured utilization percentage` 。

## <a name="cause"></a>原因

Apache Yarn 應用程式快取可能已耗用所有可用磁碟空間。 您的 Spark 應用程式可能會以效率不高的狀態執行。

## <a name="resolution"></a>解決方案

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

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
