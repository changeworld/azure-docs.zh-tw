---
title: 在 Azure HDInsight 中啟動服務時埠衝突
description: 在與 Azure HDInsight 群集交互時，對問題的步驟和可能的解決方案進行故障排除。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 4cb0d464a82d8da0a09f5391eb1d06dfacd84290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776218"
---
# <a name="scenario-port-conflict-when-starting-services-in-azure-hdinsight"></a>方案：在 Azure HDInsight 中啟動服務時埠衝突

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

服務無法啟動。

## <a name="cause"></a>原因

存在埠衝突。

## <a name="resolution"></a>解決方案

### <a name="method-1"></a>方法 1

使用以下命令獲取/終止受埠問題影響的所有正在運行的進程。

```bash
netstat -lntp | grep <port>
ps -ef | grep <service>
kill -9 <service>
```

然後開始服務。

### <a name="method-2"></a>方法 2

重新開機節點。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
