---
title: Apache Ambari UI 會顯示 Azure HDInsight 中的主機和服務
description: 當 Apache Ambari UI 問題顯示 Azure HDInsight 中的主機和服務時進行疑難排解
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943260"
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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]