---
title: Apache Ambari UI 會顯示 Azure HDInsight 中的主機和服務
description: 當 Apache Ambari UI 問題顯示 Azure HDInsight 中的主機和服務時進行疑難排解
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: e530d59feec2446679dbbfbc944e1bd0c53f3239
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546444"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>案例： Apache Ambari UI 會顯示 Azure HDInsight 中的主機和服務

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

Apache Ambari UI 可供存取，但 UI 會顯示幾乎所有的服務都已關閉，而所有的主機都會顯示遺失的信號。

## <a name="cause"></a>原因

在大部分的情況下，這是 Ambari 伺服器未在作用中前端節點上執行的問題。 檢查哪些前端節點是作用中的前端節點，並確定您的 ambari 伺服器在正確的節點上執行。 不要手動啟動 ambari 伺服器，讓容錯移轉控制器服務負責啟動正確前端節點上的 ambari 伺服器。 重新開機作用中的前端節點以強制執行容錯移轉。

網路問題也可能造成此問題。 在每個叢集節點中，查看您是否可以進行 ping `headnodehost` 。 在罕見的情況下，沒有任何叢集節點可以連接 `headnodehost` ：

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>解決方案

通常重新開機作用中的前端節點將可減輕此問題。 如果沒有，請洽詢 HDInsight 支援小組。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。