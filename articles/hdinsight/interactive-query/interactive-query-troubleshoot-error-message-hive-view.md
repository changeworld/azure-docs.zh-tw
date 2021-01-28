---
title: Apache Hive View 中未顯示的錯誤訊息-Azure HDInsight
description: Apache Hive View 中的查詢失敗，Azure HDInsight 叢集沒有任何詳細資料。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931003"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>案例： Azure HDInsight 的 Apache Hive 視圖中未顯示查詢錯誤訊息

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Hive View 查詢錯誤訊息看起來像這樣，不需要進一步的資訊：

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

有時查詢失敗的錯誤訊息可能太大，而無法顯示在 Hive View 主頁面上。

## <a name="resolution"></a>解決方案

檢查 Hive_view 右上角的 [通知] 索引標籤，以查看完整的 Stacktrace 和錯誤訊息。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]