---
title: Azure HDInsight 中的 Apache Ambari 活動訊號問題
description: 查看 Azure HDInsight 中 Apache Ambari 的各種不同原因問題
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77057068"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight 中的 Apache Ambari 活動訊號問題

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="scenario-high-cpu-utilization"></a>案例：高 CPU 使用率

### <a name="issue"></a>問題

Ambari 代理程式的 CPU 使用率很高，會導致 Ambari UI 的警示，而某些節點的 Ambari 代理程式信號會遺失。 [心跳遺失] 警示通常是暫時性的。

### <a name="cause"></a>原因

由於各種 ambari 代理程式錯誤，在罕見的情況下，您的 ambari 代理程式可能會有高（接近100）百分比的 CPU 使用率。

### <a name="resolution"></a>解決方案

1. 識別 ambari 的處理序識別碼（pid）-代理程式：

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 然後執行下列命令來顯示 CPU 使用率：

    ```bash
    top -p <ambari-agent-pid>
    ```

1. 重新開機 ambari-代理程式以減輕問題：

    ```bash
    service ambari-agent restart
    ```

1. 如果重新開機無法運作，請終止 ambari 代理程式進程，然後啟動它：

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>案例： Ambari 代理程式未啟動

### <a name="issue"></a>問題

Ambari 代理程式尚未啟動，這會導致 Ambari UI 的警示，而這些節點的 Ambari 代理程式檢測結果會遺失。

### <a name="cause"></a>原因

警示是由 Ambari 代理程式未執行所造成。

### <a name="resolution"></a>解決方案

1. 確認 ambari 的狀態-代理程式：

    ```bash
    service ambari-agent status
    ```

1. 確認容錯移轉控制器服務是否正在執行：

    ```bash
    ps -ef | grep failover
    ```

    如果容錯移轉控制器服務未執行，可能是因為發生問題而導致 hdinsight 代理程式無法啟動容錯移轉控制器。 檢查 hdinsight-代理程式記錄檔 `/var/log/hdinsight-agent/hdinsight-agent.out` 。

## <a name="scenario-heartbeat-lost-for-ambari"></a>案例： Ambari 遺失的心跳

### <a name="issue"></a>問題

Ambari 的心跳代理程式已遺失。

### <a name="cause"></a>原因

OMS 記錄會導致高 CPU 使用率。

### <a name="resolution"></a>解決方案

* 使用[AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell Cmdlet 停用 Azure 監視器記錄。
* 刪除 `mdsd.warn` 記錄檔

---

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
