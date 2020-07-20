---
title: Apache Hive 中的聯結會導致 OutOfMemory 錯誤-Azure HDInsight
description: 處理 OutOfMemory 錯誤「GC 負擔限制超過錯誤」
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895179"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>案例： Apache Hive 中的聯結會導致 Azure HDInsight 中的 OutOfMemory 錯誤

本文說明在 Azure HDInsight 叢集中使用互動式查詢元件時，針對問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

Apache Hive 聯結的預設行為是將資料表的整個內容載入記憶體中，以便在不需要執行對應/減少步驟的情況下執行聯結。 如果 Hive 資料表太大而無法放入記憶體中，則查詢可能會失敗。

## <a name="cause"></a>原因

在具有足夠大小的 hive 中執行聯結時，會發生下列錯誤：

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>解決方案

藉由設定下列 Hive 設定值，防止 Hive 將資料表載入記憶體中的聯結（改為執行對應/減少步驟）：

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>後續步驟

如果設定此值無法解決您的問題，請造訪下列其中一項 .。。

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
