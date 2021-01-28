---
title: Azure HDInsight 中的 Apache Ambari UI 502 錯誤
description: 當您嘗試存取 Azure HDInsight 叢集時發生 Apache Ambari UI 502 錯誤
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946790"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache Ambari UI 502 錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方式。

## <a name="issue"></a>問題

當您嘗試存取 HDInsight 叢集的 Apache Ambari UI 時，您會收到類似以下的訊息：「502-網頁伺服器在作為閘道或 proxy 伺服器時收到不正確回應」。

## <a name="cause"></a>原因

一般而言，HTTP 502 狀態碼表示 Ambari 伺服器無法在作用中的前端節點上正確執行。 有幾個可能的根本原因。

## <a name="resolution"></a>解決方案

在大部分的情況下，若要減輕問題，您可以重新開機作用中的前端節點。 或為前端節點選擇較大的 VM 大小。

### <a name="ambari-server-failed-to-start"></a>Ambari 伺服器無法啟動

您可以檢查 ambari 伺服器記錄，找出 Ambari 伺服器無法啟動的原因。 其中一個常見原因是資料庫一致性檢查錯誤。 您可以在此記錄檔中找到此功能： `/var/log/ambari-server/ambari-server-check-database.log` 。

如果您對叢集節點進行任何修改，請加以復原。 請一律使用 Ambari UI 來修改任何 Hadoop/Spark 相關的設定。

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 伺服器花費100% 的 CPU 使用率

在罕見的情況下，我們發現 ambari 伺服器進程會持續接近100% 的 CPU 使用率。 作為緩和措施，您可以透過 ssh 連線到作用中的前端節點，並終止 Ambari 伺服器進程並重新啟動。

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari 伺服器由 oom 終止

在某些情況下，您的前端節點會耗盡記憶體，而 Linux oom 端會開始挑選要終止的進程。 您可以藉由搜尋 AmbariServer 進程 ID （不應找到）來確認這種情況。 然後查看您 `/var/log/syslog` 的，並尋找如下所示的內容：

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

然後找出哪些處理常式正在進行記憶，並嘗試進一步的根本原因。

### <a name="other-issues-with-ambari-server"></a>Ambari 伺服器的其他問題

Ambari 伺服器很少無法處理傳入的要求，您可以查看 Ambari 伺服器記錄檔中是否有任何錯誤，以找出詳細資訊。 這種情況類似如下所示的錯誤：

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]