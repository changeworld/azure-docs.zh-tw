---
title: Apache Ambari UI 在 Azure HDInsight 中向下顯示主機和服務
description: 在 Azure HDInsight 中顯示主機和服務時，解決 Apache Ambari UI 問題
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895644"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>方案：Apache Ambari UI 在 Azure HDInsight 中向下顯示主機和服務

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache Ambari UI 是可訪問的，但 UI 顯示幾乎所有服務都已關閉，所有顯示活動訊號丟失的主機都已關閉。

## <a name="cause"></a>原因

在大多數情況下，Ambari 伺服器未在活動頭節點上運行是一個問題。 檢查哪個頭節點是活動頭節點，並確保 ambari 伺服器在正確的頭節點上運行。 不要手動啟動 ambari 伺服器，讓容錯移轉控制器服務負責在右頭節點上啟動 ambari 伺服器。 重新開機活動頭節點以強制容錯移轉。

網路問題也可能導致此問題。 從每個叢集節點中，查看是否可以 ping `headnodehost`。 很少情況下，沒有叢集節點可以連接到`headnodehost`：

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>解決方案

通常重新開機活動頭節點將緩解此問題。 如果沒有，請聯繫 HDInsight 支援小組。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
