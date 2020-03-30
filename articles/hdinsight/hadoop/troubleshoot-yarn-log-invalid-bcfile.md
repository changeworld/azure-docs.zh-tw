---
title: 無法讀取 Azure HDInsight 中的 Apache Yarn 日誌
description: 在與 Azure HDInsight 群集交互時，對問題的步驟和可能的解決方案進行故障排除。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776192"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>方案：無法讀取 Azure HDInsight 中的 Apache Yarn 日誌

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

從存儲帳戶找到的 Apache Yarn 日誌是不可讀的。 檔解析器不工作並生成以下錯誤訊息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 日誌聚合為`IndexFile`格式，檔解析器不支援該格式。

## <a name="resolution"></a>解決方案

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net`，`CLUSTERNAME`群集的名稱在哪裡。

1. 從安巴里 UI，導航到**YARN** > **配置** > **Advanced** > **高級紗線網站**。

1. 對於 WASB 存儲：的`yarn.log-aggregation.file-formats`預設值為`IndexedFormat,TFile`。 將值更改為`TFile`。

1. 對於 ADLS 存儲：的`yarn.nodemanager.log-aggregation.compression-type`預設值為`gz`。 將值更改為`none`。

1. 保存更改並重新啟動所有受影響的服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
