---
title: Azure HDInsight 中的阿帕奇 Ambari UI 502 錯誤
description: 當您嘗試訪問 Azure HDInsight 群集時，Apache Ambari UI 502 錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895766"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Ambari UI 502 錯誤

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

當您嘗試訪問 HDInsight 群集的 Apache Ambari UI 時，您會收到一條類似于"502 - Web 服務器在充當閘道或代理伺服器時收到無效回應"的消息。

## <a name="cause"></a>原因

通常，HTTP 502 狀態碼意味著 Ambari 伺服器在活動頭節點上未正確運行。 有幾個可能的根本原因。

## <a name="resolution"></a>解決方案

在大多數情況下，為了緩解問題，您可以重新開機活動頭節點。 或者為頭節點選擇更大的 VM 大小。

### <a name="ambari-server-failed-to-start"></a>Ambari 伺服器無法啟動

您可以檢查 ambari 伺服器日誌，以瞭解 Ambari 伺服器無法啟動的原因。 一個常見原因是資料庫一致性檢查錯誤。 您可以在此日誌檔中找到這一點： `/var/log/ambari-server/ambari-server-check-database.log`.

如果對叢集節點進行了任何修改，請撤銷它們。 始終使用 Ambari UI 修改任何 Hadoop/Spark 相關配置。

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari 伺服器採用 100% CPU 利用率

在極少數情況下，我們看到 ambari 伺服器進程始終具有接近 100% 的 CPU 利用率。 作為緩解措施，您可以 ssh 到活動頭節點，並終止 Ambari 伺服器進程並重新啟動它。

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>安巴里伺服器被烏姆殺手殺死

在某些情況下，您的頭節點記憶體不足，Linux oom 殺手開始選擇要殺死的進程。 您可以通過搜索不應找到的 AmbariServer 進程 ID 來驗證此情況。 然後查看您的`/var/log/syslog`，並查找類似內容：

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

然後確定哪些過程正在獲取記憶，並嘗試進一步的根本原因。

### <a name="other-issues-with-ambari-server"></a>安巴里伺服器的其他問題

很少 Ambari 伺服器無法處理傳入的請求，您可以通過查看 ambari 伺服器日誌來查找任何錯誤來查找更多資訊。 這種情況之一是這樣的錯誤：

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
