---
title: 叢集節點在 Azure HDInsight 中耗盡磁碟空間
description: 在 Azure HDInsight 中排除 Apache Hadoop 叢集節點磁碟空間問題。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: fbfd82473b68f5032d19834ac809191d498a5a67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894119"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>方案：Azure HDInsight 中的叢集節點磁碟空間不足

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

作業可能會失敗，錯誤訊息類似于：`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

或者您可能會收到類似于的阿帕奇安巴里警報`local-dirs usable space is below configured utilization percentage`： .

## <a name="cause"></a>原因

Apache Yarn 應用程式緩存可能佔用了所有可用的磁碟空間。 Spark 應用程式可能運行效率低下。

## <a name="resolution"></a>解決方案

1. 使用 Ambari UI 確定哪個節點的磁碟空間不足。

1. 確定麻煩節點中的哪個資料夾有助於大部分磁碟空間。 先將 SSH 連接到節點，`df`然後運行以列出所有裝載的磁片使用方式。 通常它是`/mnt`OSS 使用的臨時磁片。 您可以輸入資料夾，然後鍵入`sudo du -hs`以在資料夾下顯示匯總的檔案大小。 如果看到類似于`/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007`的資料夾，則意味著應用程式仍在運行。 這可能是由於 RDD 持久性或中間隨機播放檔。

1. 為了緩解此問題，請終止應用程式，這將釋放該應用程式使用的磁碟空間。

1. 要最終解決問題，請優化應用程式。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
