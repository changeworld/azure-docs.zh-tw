---
title: Apache Hive View 中未顯示錯誤訊息-Azure HDInsight
description: Apache Hive View 中的查詢失敗，Azure HDInsight 叢集沒有任何詳細資料。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 5aa03ac3537783daef87c9e7cb7d4ec58988ea9e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895208"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>案例：查詢錯誤訊息不會顯示在 Azure HDInsight 的 Apache Hive 視圖中

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Hive View 查詢錯誤訊息看起來會像這樣，而沒有進一步資訊：

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

有時候，查詢失敗的錯誤訊息可能太大，而無法顯示在 Hive 視圖的主頁面上。

## <a name="resolution"></a>解決方案

勾選 Hive_view 右上角的 [通知] 索引標籤，以查看完整的 Stacktrace 和錯誤訊息。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
