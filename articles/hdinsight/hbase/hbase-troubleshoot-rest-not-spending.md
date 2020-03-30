---
title: Apache HBase REST 未回應 Azure HDInsight 中的請求
description: 解決 Apache HBase REST 未回應 Azure HDInsight 中的請求的問題。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887167"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>方案：Apache HBase REST 不回應 Azure HDInsight 中的請求

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

Apache HBase REST 服務不回應 Azure HDInsight 中的請求。

## <a name="cause"></a>原因

這裡可能的原因是 Apache HBase REST 服務洩漏了通訊端，這在服務運行很長時間（例如幾個月）時尤為常見。 在用戶端 SDK 中，您可能會看到類似于以下錯誤的錯誤訊息：

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>解決方案

在 SSHing 到主機後，使用以下命令重新開機 HBase REST。 您還可以使用腳本操作在所有輔助節點上重新開機此服務：

```bash
sudo service hdinsight-hbrest restart
```

此命令將停止同一主機上的 HBase 區域伺服器。 您可以通過 Ambari 手動啟動 HBase 區域伺服器，或者讓 Ambari 自動重新開機功能自動復原 HBase 區域伺服器。

如果問題仍然存在，則可以將以下緩解腳本安裝為 CRON 作業，該作業每 5 分鐘在每個輔助節點上運行一次。 此緩解腳本 ping REST 服務並重新啟動它，以防 REST 服務不回應。

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
