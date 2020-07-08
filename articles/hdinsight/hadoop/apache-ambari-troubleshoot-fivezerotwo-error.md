---
title: Azure HDInsight 中的 Apache Ambari UI 502 錯誤
description: 當您嘗試存取 Azure HDInsight 叢集時，Apache Ambari UI 502 錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895766"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>案例： Azure HDInsight 中的 Apache Ambari UI 502 錯誤

本文說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

當您嘗試存取 HDInsight 叢集的 Apache Ambari UI 時，您會收到類似以下的訊息：「502-Web 服務器做為閘道或 proxy 伺服器時收到不正確回應」。

## <a name="cause"></a>原因

一般來說，HTTP 502 狀態碼表示 Ambari 伺服器未在作用中的前端節點上正確執行。 有幾個可能的根本原因。

## <a name="resolution"></a>解決方案

在大部分的情況下，若要減輕問題，您可以重新開機作用中的前端節點。 或為前端節點選擇較大的 VM 大小。

### <a name="ambari-server-failed-to-start"></a>Ambari 伺服器無法啟動

您可以檢查 ambari 伺服器記錄檔，以瞭解 Ambari 伺服器無法啟動的原因。 其中一個常見的原因是資料庫一致性檢查錯誤。 您可以在此記錄檔中找到此檔案： `/var/log/ambari-server/ambari-server-check-database.log` 。

如果您對叢集節點進行了任何修改，請將它們復原。 請一律使用 Ambari UI 來修改任何 Hadoop/Spark 相關設定。

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 伺服器佔用100% 的 CPU 使用率

在罕見的情況下，我們看到 ambari 伺服器進程持續接近100% 的 CPU 使用率。 作為緩和措施，您可以透過 ssh 連線到作用中的前端節點，並終止 Ambari 伺服器進程，然後重新開機它。

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Oom 所終止的 Ambari 伺服器-killer

在某些情況下，您的前端節點會用盡記憶體，而 Linux oom-killer 會開始挑選要終止的進程。 您可以藉由搜尋 AmbariServer 處理序識別碼（找不到）來驗證這種情況。 然後查看您 `/var/log/syslog` 的，並尋找類似如下的內容：

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

然後，找出哪些處理常式正在進行記憶，並嘗試進一步的根本原因。

### <a name="other-issues-with-ambari-server"></a>Ambari 伺服器的其他問題

Ambari 伺服器很少無法處理連入要求，您可以查看 Ambari 伺服器記錄中是否有任何錯誤，以尋找詳細資訊。 這種情況的其中一種錯誤如下：

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
